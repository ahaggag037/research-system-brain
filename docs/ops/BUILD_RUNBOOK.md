# BUILD RUNBOOK — FixHUA

> 05A §2. No secrets or private signing material here. Exact versions not yet verified are marked `UNKNOWN` with the smallest verification step. All paths PROPOSED (no code exists yet).

## 1. Required toolchain

| Tool | Version | Status / smallest verification step |
|---|---|---|
| JDK | 17 (Android Gradle Plugin 8.x baseline) | PROPOSED — verify against chosen AGP at first build (`./gradlew --version` check) |
| Android SDK | compileSdk UNKNOWN (target latest stable at build time) | UNKNOWN — pick at project init; record in `gradle/libs.versions.toml` |
| Android NDK | UNKNOWN (needed for `helper/` native + PSI JNI) | UNKNOWN — install via sdkmanager at first native build |
| Gradle | wrapper-pinned | PROPOSED — wrapper committed at project init |
| Kotlin | UNKNOWN (aligned with AGP) | UNKNOWN — set in version catalog at init |
| Rust/C++ for helper | C++ (NDK) default; Rust optional | PROPOSED — decide at helper scaffold (single decision record) |
| Host OS | Linux/macOS with Android SDK | PROPOSED |

## 2. Repository layout (proposed code tree, lives in a future code repo/branch)

```
fixhua/
├── app/                  # Android app (Kotlin): ui/ policy/ model/ obs/ act/ store/ safe/
├── helper/               # fxhelperd native daemon (NDK): src/ schema/
├── schemas/              # shared JSON schemas: obs_event, intent, helper command, config
├── bench/                # E-xx experiment harness scripts
└── gradle/               # version catalog, wrapper
```

## 3. Environment setup

1. Install JDK 17 + Android SDK (cmdline-tools); accept licenses.
2. `local.properties` (gitignored) points at SDK — never committed.
3. Clone repo; `./gradlew tasks` verifies toolchain.
4. Device: enable USB debugging; for privileged features, rooted test device (bench only, per experiment plans).

## 4. Configuration files

- `gradle/libs.versions.toml` — dependency versions (single source).
- `schemas/*.schema.json` — wire/state schemas (versioned; CI-validated).
- `app/src/main/assets/default_config.json` — shipped region tables/defaults (validated against `schemas/config.schema.json` at build).
- `helper/schema/fxhelperd.v1.json` — privileged command schema (the INV-3 boundary).

## 5. Build variants

| Variant | Purpose | Actuation |
|---|---|---|
| `debug` | development | schema-enforced flags; EXPERIMENTAL surfaces locked unless tier-3 + canary flag |
| `bench` | experiment runs (E-xx) | actuation per experiment manifest, supervised |
| `release` | production candidate | only PRODUCTION-admitted surfaces; minified; signed (boundary §9) |

## 6. Local build commands

```
./gradlew :app:assembleDebug                 # app
(cd helper && ./build.sh)                    # fxhelperd (NDK) — PROPOSED script
./gradlew :app:assembleBench :app:assembleRelease
./gradlew testDebugUnitTest                  # unit tests
./gradlew connectedBenchAndroidTest          # device tests (bench device)
```

## 7. CI build path (PROPOSED)

GitHub Actions-class pipeline: lint → schema validation → unit tests → assemble debug+release (unsigned) → artifact upload. Device stages run on a bench farm only when available (currently none — BLOCKED_BY_DEVICE_EVIDENCE).

## 8. Artifact locations

- `app/build/outputs/apk/{debug,bench,release}/`
- `helper/build/out/fxhelperd` (per ABI)
- `bench/results/<experiment>/<run>/` (device runs)

## 9. Signing boundary

- Debug/bench: debug keys (generated locally, never committed).
- Release signing: **out of scope for this package** — requires user-owned keystore; documented as UNKNOWN/external. No signing material in repo (hard rule).
- Helper token: generated on-device at install; nothing to sign with.

## 10. Installation path

```
adb install -r app/build/outputs/apk/bench/app-bench.apk
# helper (bench device, root): pushed by bench harness, socket-activated; install script PROPOSED: helper/install.sh
```

First-run validation: `FIRST_BUILD_CRITICAL_PATH.md` exit criteria (diagnosis screen renders, probes journal, overhead self-check).

## 11. Clean rebuild procedure

```
./gradlew clean && rm -rf .gradle build app/build helper/build
./gradlew :app:assembleDebug   # cold-build sanity
```

## 12. Recovery from common build failures

| Failure | Recovery |
|---|---|
| SDK/NDK mismatch | re-check §1 versions against version catalog; `sdkmanager` install missing pieces |
| Schema validation fails | fix schema or regenerate models; never bypass validation |
| Native ABI mismatch | rebuild helper for device ABI (`helper/build.sh --abi`) |
| Gradle cache corruption | clean rebuild (§11); delete `~/.gradle/caches` as last resort |
| Device not detected | adb restart; verify bench device authorized |

## 13. Artifact ↔ source verification

- Record `git rev-parse HEAD` into APK `BuildConfig` at build time (PROPOSED).
- Verify produced artifact matches documented source state: checkout SHA == BuildConfig SHA; schemas hashed into build manifest; CI reproduces checksums for release candidates (reproducible-build goal, best-effort).
