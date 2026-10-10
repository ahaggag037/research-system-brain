# STARTUP & SHUTDOWN — lifecycle contract

> 05A §7. Boot behavior is explicit, never implicit. All sequences PROPOSED (no code exists).

## 1. App start (cold)

1. FX-STORE init: VersionState check → migrations if needed → load Config (validate; fallback last-known-good) → load CapabilityRegistry, SafetyState, journal tail.
2. FX-SAFE init: governor counters zeroed; **journal-vs-live readback audit** (§6) if last shutdown was dirty or boot detected; safety mode restored from persisted state.
3. FX-OBS init: collectors start per capability registry (unknown signals re-probed lazily, never assumed).
4. FX-MODEL init: load LearnedModel (or priors+abstain); begin estimates once OBS flows.
5. FX-POLICY init: load region tables (version-checked); region = `calm/idle` baseline until first estimate.
6. FX-UI: cold open on demand only.
- **Dependency readiness order:** STORE → SAFE → OBS → MODEL → POLICY → (ACT on demand). A component starting before its dependency waits with timeout; timeout → degraded mode for that component (FAILURE_RECOVERY_MATRIX FR-04).

## 2. Privileged-service start (`fxhelperd`)

- Started on demand when: consent tier ≥2 AND at least one surface admission ≥ SAFE AND root present.
- Handshake: schema version negotiation → token + peer-UID auth → helper reports supported command set → client cross-checks vs registry.
- **Failure-to-start:** root absent → degraded mode (no privileged actuation; OBS/UI continue); auth failure → retry once, then helper marked unavailable + UI notice; auth failure burst (>3) → helper shuts down actuation path (RSK-107), SAFE alerted.
- Helper is socket-activated semantics: exits when idle beyond timeout; restartable at any time (stateless beyond stock snapshots mirror).

## 3. Boot behavior (BOOT_COMPLETED)

- Registration: optional `BOOT_COMPLETED` receiver (user setting, default ON for continuity of observation).
- Deferred start: schedule via WorkManager-class deferred task (never foreground at boot); no wakelock-held work at boot.
- First action after boot: §6 reconciliation **before** any new actuation.
- If user disabled background operation: nothing runs at boot; UI works on demand.

## 4. Graceful shutdown

- Trigger: user kill switch, consent downgrade, app halt.
- Sequence: POLICY stops emitting → SAFE persists SafetyState + flushes journal → ACT completes/rolls back in-flight intents (bounded wait) → helper told to exit → OBS collectors stop → final journal flush.
- In-flight write at shutdown: readback verification still required; if incomplete → dirty flag; reconciliation at next start.

## 5. Crash recovery

- App process crash: system restart path re-enters §1; dirty flag → reconciliation; crash counted in journal (if journal writable).
- Helper crash mid-write: client timeout → fail-closed; helper's own partial-write auto-rollback (FX-ACT contract); surface quarantined until manual or probe re-verification.
- Watchdog: none custom — Android ANR/watchdog semantics apply; FixHUA adds no always-running critical sections.

## 6. Boot/restart reconciliation (dirty-start path)

1. Read journal tail: surfaces with non-stock journaled values.
2. Readback each live surface value; compare.
3. Mismatch or expired ttl → restore stock snapshot; journal ROLLBACK entry.
4. Build fingerprint changed since last run → CapabilityRegistry reprobe; admission states reset to OBSERVED pending re-evidence (SO-7 rule).
5. Only then: normal operation resumes.

## 7. Update/restart behavior

- App update: schemas versioned; migrations run at first start (SO-2/SO-6); helper binary updated → version handshake enforces match; mismatch → helper reinstalled before actuation resumes.
- OTA / ROM update: fingerprint change triggers reprobe (§6 step 4); all EXPERIMENTAL gates re-locked.
- Rollback of the app itself: stock snapshots survive uninstall only if user chose "restore stock on uninstall" (default ON prompt); helper removed at uninstall; journal export offered.
