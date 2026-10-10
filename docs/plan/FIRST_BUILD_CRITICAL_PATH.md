# FIRST BUILD CRITICAL PATH — FB-1 "Observer"

> 05A §11. Optimized for hours-to-first-use without losing architectural integrity. All module paths PROPOSED.

## Minimum component set

M-08 (schemas) → M-07 (store) → M-01 (obs, baseline collectors) → M-06 (UI: consent tier-1 + diagnosis + journal). FX-SAFE ships in **minimal form**: journal + consent state only (no governor — nothing to govern; zero actuation in FB-1).

## Exact build order (dependency order)

1. **Schemas v1** (`schemas/obs_event.v1`, `config.v1`, `journal.v1`) — everything validates against these.
2. **FX-STORE** (M-07): DAO, config load/validate/snapshot, journal append/flush.
3. **FX-OBS baseline** (M-01): lifecycle collector → PSI poller → PSI triggers (JNI) → vmstat/counter poller → probe executor (E-01/E-12 read-only probes).
4. **FX-UI** (M-06): consent screen (tier-1), diagnosis dashboard (live PSI/lifecycle), journal viewer, export.
5. **First-run self-check**: overhead sampler (E-10 self-measurement), journal verification.

## What can be developed in parallel

- UI screens ∥ collectors ∥ store layer (schemas frozen first).
- Bench harness scripts (`bench/`: pressure allocator, N-app cycler, trace capture) ∥ all app work.

## What may be stubbed temporarily

- Perfetto diagnostic session (IF-08) — stub behind "coming soon" UI flag; not needed for FB-1 exit.
- Root probe subset (M-04 read-only PROBE) — stub returns UNSUPPORTED when absent.
- Vendor-policy detection — read-only best effort; unparseable states shown as UNKNOWN.

## What may NOT be stubbed

- Journal append-only + verification (trust anchor; T-JOURNAL).
- Consent gate (even tier-1: observation transparency).
- Truth labeling in UI (hypothesis badges — D-015).
- Schema validation (M-08) — no silent format drift.

## Mandatory tests before proceeding

T-BUILD (clean checkout build), T-INSTALL (install + first-run), T-JOURNAL, T-CONSENT(tier-1), schema unit tests. Device probes (T-E01/T-E12) run when a bench device exists — currently BLOCKED_BY_DEVICE_EVIDENCE.

## Blocking unknowns

- None for code. Device-side: U-003/U-005 (probe answers come FROM FB-1 — it is the discovery step).
- Toolchain versions UNKNOWN → resolved at project init (BUILD_RUNBOOK §1), not blocking design.

## Exit criteria for the first installable build

1. APK installs and launches on target-class device (or emulator during development).
2. Diagnosis screen shows live PSI + lifecycle data; values sane vs `dumpsys`/proc cross-check.
3. Probe matrix executes and journals results (UNKNOWN where unreadable — honesty invariant).
4. 1-hour observation session completes with self-measured overhead <1% CPU.
5. Journal export works; no permissions beyond declared; uninstall clean.

## Hours estimate (focused engineering, per 00 §0 feasibility target)

Schemas+store ≈ 1–2 h; OBS baseline ≈ 2–3 h; UI ≈ 2 h; self-check + tests ≈ 1–2 h → **≈ 6–9 hours** to installable FB-1 with tests, parallelizable to less.
