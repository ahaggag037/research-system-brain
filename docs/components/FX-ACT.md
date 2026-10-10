# Component contract — FX-ACT (incl. `fxhelperd`)

- **Stable ID:** FX-ACT
- **Proposed source path:** `app/act/` (client) + `helper/src/` (root daemon `fxhelperd`) + `helper/schema/` (versioned command schema) — PROPOSED, not implemented
- **Runtime owner:** app process (client); root daemon process (helper)
- **Lifecycle:** helper installed/started on demand when tier-2 consent + root present; socket-activated semantics; stops on kill switch or auth failure burst
- **Diagrams:** `../visuals/PRIVILEGE_BOUNDARIES.mmd`, `INCIDENT_RUNTIME.mmd`
- **Decision references:** D-009 (schema-bound helper), D-013 (single-writer)

## Contract

- **Purpose:** execute admitted `ActionIntent`s on whitelisted control surfaces (CS-1..CS-9) with readback verification and rollback journaling; provide read-only probe commands for FX-OBS capability discovery.
- **Inputs:** admitted intents from FX-SAFE (client side); schema-validated commands over local UNIX socket (helper side); capability registry (which surfaces exist/writable).
- **Outputs:** write/readback results + diffs to STORE journal; probe results to OBS; errors with typed codes to SAFE.
- **Preconditions:** for privileged commands: root present, helper installed, token + peer-UID auth pass, command in schema, parameters within typed bounds; for device_config commands: documented permission path present.
- **Postconditions:** every write verified by readback; journal entry {before, after, timestamp, intent id}; on readback mismatch → rollback attempt + SAFE alert.
- **Invariants:** helper executes **only** schema commands (INV-3); no thermal writes ever (INV-1 — no such command exists); every write journaled before+after; vendor-occupied surfaces deferred (INV-11).
- **Side effects:** intended device-surface changes only; all bounded and reversible (INV-6).
- **Dependencies:** FX-SAFE (admission), FX-STORE (journal, registry); root availability (optional — degraded mode without).
- **Failure behavior:** helper unreachable → intents fail-closed (no actuation, SAFE notified); partial write → rollback to journaled before-value; auth failure burst → helper shuts down actuation (RSK-107).
- **Recovery behavior:** rollback journal replays in reverse; stock-restore command set per surface; helper reinstall/rotate token procedure in FAILURE_RECOVERY_MATRIX.
- **Concurrency assumptions:** socket command handling serialized per surface; no concurrent writes to the same surface; client intents queued FIFO.
- **Privilege assumptions:** helper runs as root with minimal capabilities needed; app side unprivileged; socket in app-private abstract namespace with per-install token + peer-UID check.
- **Performance budget:** command latency ≪ dwell periods; helper resident footprint minimal (event-driven, no polling).
- **Configuration surface:** schema version; per-surface enable flags (default: only PRODUCTION-admitted surfaces); stock-value snapshot store.
- **Test obligations:** unit: schema validation rejects malformed/out-of-bounds; integration: mock-helper loopback; failure-injection: partial write, helper crash mid-write, auth failure; security review at BUILD_READY (RSK-107); device: E-02/E-03/E-06 execution arms.
- **Must not do:** accept arbitrary commands/paths; network access; cache credentials; write anything not in the whitelist; batch writes atomically across surfaces without journaling each; run when admission state unknown.
