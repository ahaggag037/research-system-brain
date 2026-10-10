# IMPLEMENTATION BLUEPRINT — ARCH-FX-CP-01

> 05 §7 + 05A §3. Module map (16 fields each) + stage blueprints (17 fields each). Everything here is PROPOSED; no code exists. Unverified device specifics are UNKNOWN with smallest discovery steps — nothing fabricated.

## A. Codebase / module map (05A §3)

### M-01 app/obs — FX-OBS collectors
- ID: M-01 | Path: `app/obs/` (+`app/obs/native/`) | Responsibility: sensing only (FX-OBS contract)
- Public interfaces: IF-01 event bus; probe API to ACT (read-only commands)
- Inbound deps: Android framework callbacks, kernel PSI/proc files | Outbound: FX-MODEL, FX-STORE
- Runtime owner: app process | Lifecycle: service-scoped (STARTUP §1/§3)
- Privilege: none (optional root probes via ACT) | Threads: serial executor per collector, ordered event bus
- Persistent state owned: capability registry entries (writes), experiment probe results | Logs/telemetry: OBS event stream, drop counters
- Failure modes: collector crash, signal absent, permission missing | Tests: T-E01, T-E10, T-E12, unit schema tests
- Diagrams: COMPONENT_RELATIONS, NORMAL_RUNTIME | ADRs: D-008, D-013, D-015

### M-02 app/model — FX-MODEL
- ID: M-02 | Path: `app/model/` | Responsibility: state estimation + prediction (FX-MODEL contract)
- Public interfaces: IF-02 snapshots | Inbound: IF-01, STORE learned state | Outbound: FX-POLICY, FX-STORE
- Runtime owner: app process | Lifecycle: service-scoped; online bounded learning
- Privilege: none | Threads: single update thread, lock-free read snapshots
- State owned: learned models (SO-3), ephemeral estimates (SO-1 slice) | Logs: accuracy/regret metrics
- Failure modes: corruption→priors; insufficient data→abstain | Tests: T-E09, unit estimator/calibration
- Diagrams: RUNTIME_FLOW | ADRs: D-012, D-015

### M-03 app/policy — FX-POLICY
- ID: M-03 | Path: `app/policy/` | Responsibility: region engine, intents (FX-POLICY contract)
- Public interfaces: IF-03 | Inbound: IF-02, STORE config/registry | Outbound: FX-SAFE, journal
- Runtime owner: app process | Lifecycle: event/dwell-driven evaluation loop
- Privilege: none | Threads: single evaluation loop
- State owned: ephemeral region state (SO-1 slice); region tables shipped as config | Logs: decision log
- Failure modes: stale input→hold; invalid config→snapshot | Tests: T-REGION, T-REPLAY
- Diagrams: RUNTIME_FLOW, NORMAL_RUNTIME | ADRs: D-006, D-012, D-013

### M-04 app/act + helper — FX-ACT
- ID: M-04 | Path: `app/act/`, `helper/src/`, `helper/schema/` | Responsibility: whitelisted execution + readback (FX-ACT contract)
- Public interfaces: IF-04 (in-process), IF-05 (socket schema) | Inbound: FX-SAFE | Outbound: device surfaces, STORE journal
- Runtime owner: app process (client) + root daemon | Lifecycle: on-demand, socket-activated
- Privilege: root (helper), none (client) | Threads: serialized per surface
- State owned: stock snapshots (SO-5, with SAFE), helper token | Logs: actuation journal legs
- Failure modes: FR-05/06/07/12 | Tests: T-SCHEMA, T-AUTH, T-PARTIAL, T-E02/E-03/E-06 arms
- Diagrams: PRIVILEGE_BOUNDARIES, INCIDENT_RUNTIME | ADRs: D-009, D-013

### M-05 app/safe — FX-SAFE
- ID: M-05 | Path: `app/safe/` | Responsibility: admission, governor, journal, kill switch (FX-SAFE contract)
- Public interfaces: IF-04 in, veto/rollback out | Inbound: FX-POLICY, ACT readbacks, UI consent | Outbound: FX-ACT, FX-STORE, FX-UI
- Runtime owner: app process | Lifecycle: first init, last stop
- Privilege: none | Threads: admission on policy loop; serialized journal
- State owned: admission states (SO-7 slice), safety state (SO-5), journal (SO-8) | Logs: the journal itself
- Failure modes: FR-04 (journal), FR-10 guards | Tests: T-OSC, T-ENERGY, T-JOURNAL, T-CONSENT
- Diagrams: PRIVILEGE_BOUNDARIES, FAILURE_RECOVERY | ADRs: D-010, D-012

### M-06 app/ui — FX-UI
- ID: M-06 | Path: `app/ui/` | Responsibility: consent, diagnosis, journal view, guidance, kill switch (FX-UI contract)
- Public interfaces: IF-07 | Inbound: STORE/SAFE/OBS | Outbound: consent commands, diagnostic requests
- Runtime owner: app UI thread | Lifecycle: on-demand
- Privilege: none | Threads: UI + flows
- State owned: UI prefs (SO-2 slice) | Logs: local UI events
- Failure modes: store unavailable→cached+banner | Tests: T-CONSENT, screenshot/state tests
- Diagrams: UI_FLOW | ADRs: D-008, D-013

### M-07 app/store — FX-STORE
- ID: M-07 | Path: `app/store/` | Responsibility: all persistence, schemas, migrations (FX-STORE contract)
- Public interfaces: IF-06 DAO | Inbound: all components | Outbound: all components
- Runtime owner: app process | Lifecycle: first init
- Privilege: none | Threads: serialized writers, snapshot reads
- State owned: SO-2..SO-8 | Logs: write metrics
- Failure modes: FR-03/04/13 | Tests: T-CORRUPT, migration tests
- Diagrams: COMPONENT_RELATIONS | ADRs: D-010, D-015

### M-08 schemas/ — shared contracts
- ID: M-08 | Path: `schemas/` | Responsibility: versioned JSON schemas (obs_event, intent, config, helper command)
- Interfaces: consumed at build (codegen/validation) | Deps: none | Owner: build
- Privilege: n/a | Threads: n/a | State: schema versions | Logs: CI validation
- Failure modes: invalid schema blocks build | Tests: schema validation in CI
- Diagrams: n/a | ADRs: D-009

### M-09 bench/ — experiment harness
- ID: M-09 | Path: `bench/` | Responsibility: E-xx scripts (pressure inducer, N-app cycler, trace capture)
- Interfaces: adb/device control | Deps: device | Owner: bench operator
- Privilege: bench root | State: results dirs | Logs: result bundles
- Failure modes: device disconnect | Tests: self-check scripts
- Diagrams: BUILD_RELEASE_FLOW | ADRs: D-015

## B. Stage blueprints (05 §7 — 17 fields each)

### STAGE S1 — FB-1 "Observer" (first useful build)

- **Purpose:** diagnostic value immediately; reveal device truth; run E-01/E-03/E-05/E-10/E-12 probes; foundation for everything (02 §8).
- **Dependencies:** toolchain resolved (BUILD_RUNBOOK §1); schemas v1 frozen for obs_event/config.
- **Files/modules:** M-01, M-06, M-07, M-08; M-04 read-only PROBE subset optional (root probe).
- **Classes/interfaces:** ObsEvent bus (IF-01), Store DAO (IF-06), UI screens (diagnosis, consent tier-1, journal).
- **Data models:** SO-2 config (subset), SO-7 registry, SO-8 journal, SO-6 versions.
- **State transitions:** install → tier-1 consent → probing → observing → (diagnostic session on demand).
- **Algorithms/pseudocode:** PSI trigger registration (some 70ms/1s, full 700ms/1s baselines from S-002, tunable); poll loop ≤1 Hz; probe matrix executor.
- **Concurrency:** per M-01/M-07 thread rules.
- **Privilege requirements:** none mandatory; optional root probe via M-04 subset.
- **Error handling:** per FR-01/02/03/04/08.
- **Rollback:** uninstall clean (no writes exist).
- **Test obligations:** T-BUILD, T-INSTALL, T-JOURNAL, T-CONSENT(tier-1), schema unit tests.
- **Build obligations:** debug+bench variants assemble; schema validation in build.
- **Real-device validation:** E-01/E-10/E-12 when device arrives (BLOCKED_BY_DEVICE_EVIDENCE now).
- **Entry criteria:** STATE C package complete (this file + contracts).
- **Exit criteria:** FB-1 installs; diagnosis screen shows live pressure/lifecycle data; probes journal results; overhead self-check <1% CPU; journal exportable.
- **Parallelizable work:** UI screens ∥ collectors ∥ store layer ∥ bench harness scripts.

### STAGE S2 — FB-2 "Canary actuation"

- **Purpose:** first safe actuation: CS-1 boosts, CS-3/CS-4 device_config levers behind tier-2 consent; shadow mode first.
- **Dependencies:** S1 shipped; helper built + security-reviewed (RSK-107); SAFE governor implemented.
- **Files/modules:** + M-03, M-04 (full), M-05.
- **Classes/interfaces:** IF-03/04/05; admission registry; governor counters; rollback executor.
- **Data models:** + SO-1 runtime slices, SO-5 safety/rollback, full SO-2.
- **State transitions:** shadow (intents logged, not executed) → canary (subset surfaces, supervised) → normal tier-2.
- **Algorithms:** region engine v1 (calm/rising/critical × idle/launch/sustained), hysteresis/dwell, rate limits, energy accounting.
- **Concurrency:** per M-03/04/05 rules; socket serialization.
- **Privilege:** root for CS-1/CS-2; device_config permissions for CS-3/CS-4.
- **Error handling:** FR-05/06/07/10/12.
- **Rollback:** stock snapshots + rollback journal replay; kill switch.
- **Tests:** T-SCHEMA, T-AUTH, T-PARTIAL, T-OSC, T-REGION, T-REPLAY, T-CONSENT; device T-E02/T-E04 gates for PRODUCTION promotion.
- **Build obligations:** bench variant with experiment manifests.
- **Device validation:** E-02, E-03, E-04, E-05 runs.
- **Entry criteria:** S1 exit + helper security review done.
- **Exit criteria:** canary runs stable 48h bench; E-02/E-04 verdicts recorded; no FR-10 events.
- **Parallelizable:** helper ∥ policy engine ∥ SAFE governor ∥ bench harnesses E-02/E-04.

### STAGE S3 — FB-3 "Daily-usable"

- **Purpose:** full adaptive plane: prediction-armed low-regret actions, thermal shaping, region tables tuned per device class.
- **Dependencies:** S2 stable; E-09 accuracy evidence (from FB-1/FB-2 collected data).
- **Files/modules:** + M-02 full prediction; CS-5/CS-6/CS-7/CS-8 adapters.
- **Classes/interfaces:** PredictionSet consumption in POLICY; regret accounting.
- **Data models:** SO-3 full learned state lifecycle.
- **State transitions:** prediction armed per-surface after E-09 pass; thermal shaping after E-08 pass.
- **Algorithms:** Markov + context features; calibration; abstention policy; region table v2 (vm regions per E-06).
- **Concurrency:** unchanged (single model thread).
- **Privilege:** root surfaces per admission; all tier-2.
- **Error handling:** + regret-bound auto-disable (INV-12).
- **Rollback:** per-surface rollback + model reset.
- **Tests:** T-E09, T-REG, T-ENERGY(device), T-CORRUPT full.
- **Build obligations:** release candidate variant.
- **Device validation:** 7-day soak; SLO delta report vs stock.
- **Entry criteria:** S2 exit + E-09/E-08/E-06 verdicts.
- **Exit criteria:** DAILY_USE_READY criteria (READY_STATES.md).
- **Parallelizable:** model work ∥ CS-5/6/7/8 adapters ∥ soak harness.

### STAGE S4 — EXPERIMENTAL track (parallel, never blocking)

- **Purpose:** E-11 lmkd retune canary; ARCH-C feasibility watch.
- **Dependencies:** S2 exit; supervised bench device; explicit tier-3.
- **Everything else:** per E-11 definition; disposition recorded in catalog (`../research/RISK_TRANSMUTATION_CATALOG.md` RT-03).
- **Exit criteria:** verdict recorded; either PRODUCTION proposal with evidence or research-only archive.
