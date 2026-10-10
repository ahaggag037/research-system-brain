# Component contract — FX-POLICY

- **Stable ID:** FX-POLICY
- **Proposed source path:** `app/policy/` (region engine + governor-facing intents) — PROPOSED, not implemented
- **Runtime owner:** app process
- **Lifecycle:** evaluates on state-change events and dwell expiries; persistent region tables in STORE
- **Diagrams:** `../visuals/RUNTIME_FLOW.mmd`, `NORMAL_RUNTIME.mmd`
- **Decision references:** D-006, D-012, D-013

## Contract

- **Purpose:** convert `StateEstimate`/`PredictionSet` into bounded **action intents** that maximize user-visible SLOs under hysteresis, dwell, rate, and energy constraints. Sole issuer of intents.
- **Inputs:** state estimates + predictions (FX-MODEL); capability registry + admission states (FX-SAFE/STORE); config (region tables, thresholds); energy accounting (FX-ACT readbacks via STORE).
- **Outputs:** `ActionIntent` stream to FX-SAFE (never directly to ACT); policy decision log entries to STORE journal.
- **Preconditions:** state estimate available; admission states loaded; config valid (schema-checked).
- **Postconditions:** every intent carries {control surface, parameters within validated bounds, ttl, justification region, expected SLO effect class}; intents expire if not admitted within ttl.
- **Invariants:** no intent for surfaces below admission level required by mode (INV/pipeline); dwell/rate limits enforced before emission; energy budget checked before emission; region tables are data (versioned config), not code.
- **Side effects:** none on device directly (intents only).
- **Dependencies:** FX-MODEL, FX-SAFE, FX-STORE.
- **Failure behavior:** missing/stale estimate → hold current region, no new intents (fail-quiet, never fail-active); invalid config → fall back to last known-good config snapshot.
- **Recovery behavior:** config rollback to snapshot; region reset to `calm/idle` baseline; oscillation guard handled with SAFE (INV-10).
- **Concurrency assumptions:** single evaluation loop; intents ordered; no blocking I/O on the loop.
- **Privilege assumptions:** none (emits intents; execution is ACT).
- **Performance budget:** evaluation ≤ few ms; steady-state wakeups ≤ OBS event rate.
- **Configuration surface:** region tables (per device class), thresholds, dwell times, rate limits, energy budget — schema-validated, versioned, snapshot-restorable.
- **Test obligations:** unit: region transition matrix incl. hysteresis edges; simulation: recorded-trace replay produces expected intent sequences; failure-injection: stale inputs, config corruption; device: E-02/E-04 policy arms.
- **Must not do:** write device surfaces; bypass SAFE; emit intents faster than rate limits; use unbounded parameters; optimize synthetic metrics (R-045).
