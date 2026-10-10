# ARCHITECTURE — ARCH-FX-CP-01 "CONTINUUM"

> Canonical home for: selected product thesis, architecture, subsystem responsibilities overview, privilege model, control/decision logic, runtime flow overview. Decisions: D-006 (category), D-007 (selection), D-008..D-015. Safety rules live in `SAFETY_INVARIANTS.md`.

## 1. Product thesis

Damage concentrates at **transitions and contention spikes**; stock control is reactive, per-resource, uncoordinated (PROBLEM_MODEL §1). FixHUA is a closed-loop **continuity control plane**: sense early (PSI triggers + lifecycle events), model state and near-future workload, decide under SLO objectives with hysteresis/dwell/rate limits, act through whitelisted bounded surfaces, and govern everything through an admission pipeline and audit. Predictions arm **only low-regret actions** (D-012). Vendor policy is detected and worked **with**, never silently fought (D-013).

## 2. Component architecture

```
            ┌──────────────────────────── FX-SAFE ───────────────────────────┐
            │  admission pipeline · governor (dwell/rate/energy) · journal    │
            └───────┬───────────────────────────────────────┬────────────────┘
                    │ admits/governs                         │ audits
  sensors → FX-OBS ──→ FX-MODEL ──→ FX-POLICY ──→ FX-ACT ──→ device surfaces
  (PSI, lifecycle,    (state est.,  (SLO policy,   (whitelist  (uclamp, adj,
   counters, vendor    prediction)   regions,       adapters    device_config,
   policy)                            hysteresis)    via helper) vm, io, warm)
                    │              │              │
                 FX-STORE (config, capability registry, models, journal)
                    │
                 FX-UI (consent tiers, diagnosis, action journal, kill switch)
```

| Component | Responsibility (unique — ownership rule 05A §4) |
|---|---|
| FX-OBS | All sensing: PSI triggers, ≤1 Hz counters, lifecycle/bucket/freeze events, vendor-policy state, on-demand Perfetto sessions. Never actuates. |
| FX-MODEL | State estimation (workload phase, pressure regime, thermal trend) + next-app prediction with confidence/abstention. Never touches device surfaces. |
| FX-POLICY | SLO-driven decisions: operating-region selection, hysteresis, dwell, rate limiting, energy budget accounting. Emits action intents only. |
| FX-ACT | Executes intents through whitelist adapters via schema-bound root helper `fxhelperd` (or unprivileged paths where possible). Readback-verifies every write. |
| FX-SAFE | Admission pipeline per control surface (UNKNOWN→OBSERVED→USEFUL→SAFE→PRODUCTION; EXPERIMENTAL gate), governor (dwell/rate/energy/oscillation guards), kill switch, immutable action journal. Vetoes any intent violating invariants. |
| FX-UI | Consent tiers, diagnosis views, action journal visibility, guided vendor-settings flow, one-tap disable. |
| FX-STORE | Persistent + runtime state: config, capability registry, learned models, journal, experiment state. Single writer per object. |

## 3. Control surfaces (whitelist — nothing else may be actuated)

| ID | Surface | Mechanism base | Admission ceiling | Notes |
|---|---|---|---|---|
| CS-1 | cgroup `cpu.uclamp.min` transient boost | S-004 | PRODUCTION after E-02 | bounded magnitude/duration |
| CS-2 | `oom_score_adj` timed nudges on cached set | S-002 | gated by E-03 | tolerate AMS overwrite |
| CS-3 | `device_config activity_manager max_cached_processes` | S-013 | PRODUCTION after E-04 | documented override |
| CS-4 | `device_config activity_manager use_freezer` policy alignment | S-013 | PRODUCTION after E-05 | documented override |
| CS-5 | vm tunables operating regions (swappiness, extra_free_kbytes, watermark_scale_factor) | S-017/S-018 | PRODUCTION after E-06 | region table, not magic values |
| CS-6 | background I/O deprioritization (ionice / io cgroup weight) | S-008 | PRODUCTION after E-07 | scheduling only, no data risk |
| CS-7 | page-cache warm for predicted next app | S-015/S-016 | PRODUCTION after E-09 | bounded size; idle-only |
| CS-8 | advisory load shaping before thermal throttle | S-005 | PRODUCTION after E-08 | never touches thermal config (INV-1) |
| CS-9 | `ro.lmk.*` retune + lmkd restart | S-002/S-003 | **EXPERIMENTAL only** (E-11 canary) | never default |
| CS-10 | vendor-settings guidance (battery opt, app-launch manager) | S-010 | PRODUCTION (UI guidance; E-12 detection) | user executes; app never silently changes |

Privileged writes (CS-1, CS-2, CS-5, CS-6, CS-7, CS-9) go through `fxhelperd`; CS-3/CS-4 via documented `device_config` (root or `adb shell` class grant); CS-10 is UI-only.

## 4. Control logic (decision core)

- **Operating regions** (02 §6): POLICY computes region from OBS state — e.g., `pressure_regime ∈ {calm, rising, critical}` × `interaction_phase ∈ {idle, launch, sustained}` × `thermal_trend ∈ {cool, warming, hot}`. Each region maps to a bounded parameter set (region table in FX-POLICY contract). No universal magic values: every parameter has help/hurt/refuse/reverse conditions.
- **Hysteresis/dwell**: transitions into a region require signal persistence ≥ dwell(T); exits require counter-persistence; rate limits cap actuation frequency; anti-windup on adaptive terms (control discipline per S-019).
- **Prediction gating**: FX-MODEL emits (candidate_set, confidence). POLICY arms only if confidence ≥ threshold AND action ∈ low-regret set {CS-7 bounded warm, CS-2 protect nudge, CS-1 pre-arm} (D-012). Online regret accounting feeds back (E-09).
- **Energy budget**: every action carries estimated energy cost; budget breach auto-degrades to observation-only (RSK-106).
- **Vendor coexistence**: if vendor boost/policy active on a surface, FX-ACT defers (single-writer rule, RSK-101); gaps only are filled (D-013).

## 5. Privilege model (D-009)

- App process: unprivileged Android app. Direct power: standard APIs (UsageStats with user grant, AppOps state reads, UI).
- `fxhelperd`: native root daemon; **only** fixed, versioned, schema-validated commands over local UNIX socket; per-install token + peer-UID auth; no shell passthrough, no network, no eval. Each adapter maps to one schema command with typed bounds.
- Absent root → degraded mode: OBS (reduced), UI diagnosis/guidance, no privileged actuation. Product still useful (FB-1 ships exactly this plus root-optional probes).

## 6. Runtime flow overview

Normal loop: OBS events → MODEL state update (≤1 Hz steady, event-driven bursts) → POLICY evaluate (on state change or dwell expiry) → SAFE admit/govern → ACT execute+readback → journal. Incident paths (oscillation, abort, crash, boot) in `../lifecycle/STARTUP_SHUTDOWN.md` and `../ops/FAILURE_RECOVERY_MATRIX.md`; end-to-end traces in `../integration/RUNTIME_TRACES.md`.

## 7. Rollout architecture (D-008)

- **FB-1 Observer** (hours): OBS + STORE + UI diagnosis; zero actuation; runs E-01/E-03/E-05/E-10/E-12 probes.
- **FB-2 Canary actuation**: SAFE-gated CS-1/CS-3/CS-4 behind consent tier 2; shadow mode first (intents logged, not executed).
- **FB-3 Daily-usable**: MODEL prediction armed (low-regret only), full region policy, E-09 loop, CS-9 remains EXPERIMENTAL-locked.

## 8. Why each subsystem earns its place (01 §7)

- FX-OBS: without early signals the plane is blind (lmkd-grade reactivity); cost bounded by trigger design (E-10).
- FX-MODEL: state-blind tweaks are the rejected alternative (ARCH-A); prediction value gated by E-09 — if falsified, MODEL degrades to heuristics and POLICY still functions.
- FX-POLICY: coordination is the thesis; hysteresis/dwell prevent oscillation (attack #4).
- FX-ACT: whitelisting + readback is what makes root justifiable (01 §3/§6).
- FX-SAFE: admission pipeline is the difference between engineering and superstition (R-043); kill switch bounds blast radius.
- FX-UI: consent + visibility are trust requirements (RSK-110) and carry vendor-guidance value standalone.
- FX-STORE: continuity of config/models/journal across reboot; corruption behavior defined per object (DATA_STATE_MODEL).
