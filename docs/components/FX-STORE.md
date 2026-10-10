# Component contract — FX-STORE

- **Stable ID:** FX-STORE
- **Proposed source path:** `app/store/` — PROPOSED, not implemented
- **Runtime owner:** app process
- **Lifecycle:** initialized first at app start; single writer per object class
- **Diagrams:** `../visuals/COMPONENT_RELATIONS.mmd`
- **Decision references:** D-010 (capability registry), D-015

## Contract

- **Purpose:** own all state: persistent config, device capability registry, admission states, learned models, action journal, experiment state, stock-value snapshots. Schemas in `../integration/DATA_STATE_MODEL.md`.
- **Inputs:** writes from OBS (probe results), MODEL (learned state), SAFE (journal, admission), ACT (readbacks, snapshots), UI (consent, config).
- **Outputs:** reads/snapshots to all components; export bundle for diagnostics (user-initiated).
- **Preconditions:** app-private storage available.
- **Postconditions:** schema-validated writes; versioned migrations; append-only journal.
- **Invariants:** single writer per object; journal append-only; config snapshots retained (last-known-good); no secrets beyond the on-device helper token (app-private, never exported).
- **Side effects:** disk I/O bounded (batched journal flush).
- **Dependencies:** Android storage (Room/Proto DataStore class — exact libs in BUILD_RUNBOOK, versions UNKNOWN-marked).
- **Failure behavior:** corruption per object → defined behavior in DATA_STATE_MODEL §6 (journal: preserve tail; config: restore snapshot; models: reset to priors).
- **Recovery behavior:** restore-from-snapshot paths; journal tail salvage; factory reset option via UI.
- **Concurrency assumptions:** serialized writers; snapshot reads lock-free where possible.
- **Privilege assumptions:** none.
- **Performance budget:** writes off hot path of policy loop; journal flush ≤ 1 Hz batched.
- **Configuration surface:** retention limits, export, reset.
- **Test obligations:** migration tests per schema version; corruption-injection tests; journal append-only verification; backup/restore behavior tests.
- **Must not do:** silently rewrite history (05 §4); store personal content; export the helper token; block the policy loop on disk.
