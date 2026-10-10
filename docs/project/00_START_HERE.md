# 00_START_HERE — FixHUA Project Entry

> Canonical project entry document (05B §3). Read this after root `AGENTS.md`. Reading time target: < 5 minutes.

## What FixHUA is

FixHUA is an **adaptive continuity control plane** for Huawei-class Android phones (architecture `ARCH-FX-CP-01 "CONTINUUM"`, decisions D-006/D-007). It observes device pressure and app-lifecycle signals, models the current and near-future state, and applies **bounded, state-aware, reversible** interventions through whitelisted control surfaces — under a strict safety governor — to keep transitions fast, frames smooth, and apps alive across multitasking.

It is **not** a RAM booster/cleaner, not a static tweak pack, not a kernel mod, not a ROM mod. Root is used only as a scoped capability behind a schema-bound helper (D-009); an unprivileged degraded mode exists.

## What problem it solves

User-visible failures on the target class concentrate at **transitions and contention spikes** (full analysis: `PROBLEM_MODEL.md`):

- **FF-1** transition latency — cold/warm start and resume delays.
- **FF-2** jank — frame deadline misses from app-side, SurfaceFlinger, scheduling, I/O, memory stalls.
- **FF-3** continuity loss — background kills (lmkd, vendor cleaners, freezer, standby buckets) causing reloads and state loss.
- **FF-4** sustained instability — thermal throttling and workload drift degrading experience over time.

Stock control is **reactive, per-resource, and uncoordinated**; FixHUA's thesis is predictive, state-aware, coordinated, bounded intervention with explicit admission control.

## What architecture is selected

`ARCH-FX-CP-01`: pipeline **FX-OBS → FX-MODEL → FX-POLICY → FX-ACT**, supervised by **FX-SAFE**, persisted by **FX-STORE**, surfaced by **FX-UI**. Details: `../architecture/ARCHITECTURE.md`; contracts: `../components/FX-*.md`; rejected alternatives: `../architecture/ALTERNATIVES.md`.

## What is implemented

**Nothing.** No code exists yet. Every module path in this package is **PROPOSED**. See `../ops/READY_STATES.md`: current status is `ARCHITECTURE_READY` satisfied, `IMPLEMENTATION_READY` satisfied (FULLY_SPECIFIED), everything beyond = `BLOCKED_BY_DEVICE_EVIDENCE` (no physical device in the design environment — D-015).

## What is verified on device

**Nothing.** All device-dependent claims are HYPOTHESIS with ready-to-run falsification experiments E-01..E-12 (`../../.v10/working_memory/EXPERIMENT_REGISTRY.md`; strategy: `../research/EXPERIMENT_STRATEGY.md`).

## What is unknown

Critical unknowns U-003..U-007 (per-device control writability, SLO budgets, EMUI variance, thermal predictability, predictor accuracy) — listed with gating experiments in `.v10/state/PROJECT_STATE.json` (`critical_unknowns`).

## What the next milestone is

Per `.v10/working_memory/CHECKPOINT.md` — currently: **FB-1 "Observer"** build (zero actuation, diagnostic value, runs E-01/E-03/E-05/E-10/E-12). Plan: `../plan/FIRST_BUILD_CRITICAL_PATH.md` and `../plan/FAST_BUILD_PLAN.md`.

## Where to read for each subsystem

| Subsystem | Contract | Decisions |
|---|---|---|
| FX-OBS (telemetry) | `../components/FX-OBS.md` | D-008 |
| FX-MODEL (state + prediction) | `../components/FX-MODEL.md` | D-012 |
| FX-POLICY (decision engine) | `../components/FX-POLICY.md` | D-006, D-013 |
| FX-ACT (privileged adapters) | `../components/FX-ACT.md` | D-009 |
| FX-SAFE (admission + governor) | `../components/FX-SAFE.md` | D-010 |
| FX-UI (consent + diagnosis) | `../components/FX-UI.md` | D-008 |
| FX-STORE (state) | `../components/FX-STORE.md` | D-010 |

Cross-cutting: interfaces `../integration/INTERFACES.md`, data/state `../integration/DATA_STATE_MODEL.md`, lifecycle `../lifecycle/STARTUP_SHUTDOWN.md`, build `../ops/BUILD_RUNBOOK.md`, failures `../ops/FAILURE_RECOVERY_MATRIX.md`, tests `../ops/TEST_MATRIX.md`, visuals `../visuals/`.

## What invariants must not be violated

`../architecture/SAFETY_INVARIANTS.md` — summarized: never write thermal protection config; no account/banking/DRM/integrity/anti-tamper bypasses; no arbitrary-root endpoints; no production actuation from controls below SAFE admission on the device; observation is always safe to fall back to; user consent gates actuation tiers; telemetry budget <1% CPU.

## Knowledge-domain map (05 §2)

| Domain | Canonical home |
|---|---|
| Problem definition / goals / success model / non-goals | `PROBLEM_MODEL.md` |
| Verified platform facts; assumptions; unknowns | `TRUTH_REGISTER.md` |
| Product thesis; architecture; control logic; privilege model; runtime flows; safety invariants | `../architecture/ARCHITECTURE.md`, `SAFETY_INVARIANTS.md` |
| Alternatives and why they lost | `../architecture/ALTERNATIVES.md` |
| Subsystem responsibilities | `../components/FX-*.md` |
| Interfaces/contracts; data/state models; runtime flows (E2E) | `../integration/` |
| Failure handling; recovery/rollback | `../ops/FAILURE_RECOVERY_MATRIX.md` |
| Measurement strategy; experiment strategy | `../research/MEASUREMENT_STRATEGY.md`, `EXPERIMENT_STRATEGY.md` |
| Build/release process; signing/secrets boundaries | `../ops/BUILD_RUNBOOK.md` |
| Implementation order; code/module map | `../plan/IMPLEMENTATION_BLUEPRINT.md` |
| Current project state | `../../.v10/state/PROJECT_STATE.json` |
| Open questions | `TRUTH_REGISTER.md` §Unknowns |
| Deferred/high-potential ideas | `../research/RISK_TRANSMUTATION_CATALOG.md` |
| Rejected ideas | `../research/REJECTED_IDEAS.md` |
| Future-model operating instructions | root `AGENTS.md` |

## Operating rules for future models

1. Truth-state discipline — every consequential claim carries VERIFIED_FACT / IMPLEMENTED / TESTED / PROPOSED / EXPERIMENTAL / HYPOTHESIS / REJECTED / DEFERRED / UNKNOWN.
2. Contradictions: do not guess. Apply the truth hierarchy in `TRUTH_REGISTER.md` §2, record in `.v10/working_memory/CONTRADICTION_REGISTER.md`, repair in the same change.
3. Privileged changes are admitted only through the pipeline in `../architecture/SAFETY_INVARIANTS.md` §3 (UNKNOWN→OBSERVED→USEFUL→SAFE→PRODUCTION).
4. Same-change documentation rule (05B §7): a code change altering architecture, interfaces, behavior, privilege, safety, state schema, build, or phase updates the affected canonical docs and machine-readable state **in the same change**. Stale docs are defects.
5. Never guess missing device facts — mark UNKNOWN and define the smallest discovery step (05 §7).
