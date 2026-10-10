# Component contract — FX-SAFE

- **Stable ID:** FX-SAFE
- **Proposed source path:** `app/safe/` (admission registry, governor, journal) — PROPOSED, not implemented
- **Runtime owner:** app process (journal appended via STORE)
- **Lifecycle:** always resident with app service; first component initialized, last to stop
- **Diagrams:** `../visuals/PRIVILEGE_BOUNDARIES.mmd`, `FAILURE_RECOVERY.mmd`
- **Decision references:** D-010 (pipeline), D-012, D-015

## Contract

- **Purpose:** control admission pipeline (UNKNOWN→OBSERVED→USEFUL→SAFE→PRODUCTION + EXPERIMENTAL gate), runtime governor (dwell/rate/energy/oscillation guards), kill switch, and the immutable audit journal. Vetoes any intent violating invariants.
- **Inputs:** intents from FX-POLICY; capability/admission state from STORE; ACT readback results; consent tier from FX-UI; self-metrics (oscillation counters, energy accounting).
- **Outputs:** admitted/rejected intent decisions to FX-ACT/POLICY; journal records to STORE; safety state (NORMAL / DEGRADED / OBSERVATION_ONLY) to UI and POLICY.
- **Preconditions:** config + admission registry loaded; journal writable.
- **Postconditions:** no intent reaches ACT without admission; every decision journaled; safety state transitions journaled.
- **Invariants:** INV-1..INV-12 enforced here at runtime; kill switch halts actuation within one evaluation cycle; journal is append-only.
- **Side effects:** may command ACT to execute rollback sequences (stock restore) on abort paths.
- **Dependencies:** FX-STORE, FX-ACT (for rollback execution), FX-UI (consent).
- **Failure behavior:** journal write failure → block actuation (fail-closed); admission state unknown for a surface → treated as UNKNOWN (no actuation).
- **Recovery behavior:** on startup, reconcile journal vs live surface values (readback audit); mismatch → restore stock, mark surface suspect, alert.
- **Concurrency assumptions:** admission checks synchronous on policy loop; governor counters atomic; journal writes serialized.
- **Privilege assumptions:** none itself (delegates privileged execution to ACT).
- **Performance budget:** admission check ≪ intent rate; journal I/O batched without losing append-only semantics.
- **Configuration surface:** admission states per surface per device; governor limits (dwell/rate/energy); kill switch; EXPERIMENTAL unlock flags (tier-3).
- **Test obligations:** unit: pipeline transitions, veto rules, governor guards; failure-injection: journal loss, oscillation storm simulation, consent downgrade mid-run; integration: end-to-end intent rejection path; device: gates E-02/E-04 promotions.
- **Must not do:** promote a surface without recorded evidence; modify journal history; actuate below admission; allow EXPERIMENTAL surfaces without tier-3 consent + canary flags.
