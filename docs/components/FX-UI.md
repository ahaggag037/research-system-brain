# Component contract — FX-UI

- **Stable ID:** FX-UI
- **Proposed source path:** `app/ui/` — PROPOSED, not implemented
- **Runtime owner:** app process (UI thread)
- **Lifecycle:** on-demand activity; persistent status via notification only when user opts in
- **Diagrams:** `../visuals/UI_FLOW.mmd`
- **Decision references:** D-008, D-013 (guidance flow), RSK-110

## Contract

- **Purpose:** consent management (tier 1 diagnosis / tier 2 actuation / tier 3 experimental), diagnosis visualization (pressure/jank/continuity findings), action journal visibility, guided vendor-settings flow (CS-10), one-tap kill switch.
- **Inputs:** FX-STORE state (findings, journal, admission states); FX-SAFE safety state; FX-OBS diagnostic bundles.
- **Outputs:** consent tier changes to SAFE; user-initiated diagnostic session requests to OBS; guided-settings intents (system settings screens).
- **Preconditions:** none for tier 1; tiers 2/3 require explicit informed consent screens with risk statements.
- **Postconditions:** consent state persisted; kill switch takes effect within one evaluation cycle (INV-7).
- **Invariants:** honest truth display — device-unvalidated claims shown as hypotheses (D-015); no dark patterns; experimental features visually fenced.
- **Side effects:** launches system settings screens for user-executed vendor changes; never silently changes vendor settings (D-013).
- **Dependencies:** FX-STORE, FX-SAFE, FX-OBS.
- **Failure behavior:** store unavailable → read-only cached snapshot + banner; kill switch must still function (local flag honored by SAFE at next cycle).
- **Recovery behavior:** resync on store recovery; consent state never auto-elevates after data loss (defaults down).
- **Concurrency assumptions:** UI thread + state Flows; no blocking disk on UI thread.
- **Privilege assumptions:** none.
- **Performance budget:** cold open < 1 s on target class; no background cost when closed (beyond opted-in notification).
- **Configuration surface:** consent tiers, notification opt-in, diagnostic session trigger, journal filters, model reset, data reset.
- **Test obligations:** UI tests: consent flows, kill switch propagation, journal rendering; screenshot/state tests for safety banners; device: FB-1 diagnosis acceptance.
- **Must not do:** hide actuation from the journal; pre-check consent boxes; display unverified "improvement %" claims; nag loops for tier elevation.
