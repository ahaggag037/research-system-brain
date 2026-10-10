# PROBLEM MODEL — FixHUA

> Canonical home for: problem definition, user-visible goals, success model, non-goals. Evidence classification per truth policy (D-015).

## 1. Problem definition

Huawei-class Android phones (EMUI / dual-framework HarmonyOS ≤4, Arm big.LITTLE, kernel ≥4.19 — D-014) exhibit user-visible degradation that stock per-resource reactive control does not prevent:

- transitions (launch, switch, resume) are slower and more variable than hardware allows;
- frames miss deadlines in bursts (stutter) rather than uniformly;
- multitasking continuity breaks: apps reload, lose state, or are killed by layered policies (lmkd, EMUI background management, cached-app freezer, standby buckets, phantom process limits);
- sustained workloads drift: thermal throttling and background contention erode responsiveness over minutes.

### Failure families (VERIFIED mechanism base: sources S-002..S-021; quantitative extents on target = HYPOTHESIS pending E-01..E-12)

- **FF-1 Transition latency.** Cold start = process spawn + dex/ART + I/O page-in + first frames; warm/hot resume = scheduling + possible unfreeze + GC catch-up. Deterministic levers exist (uclamp placement/freq hints S-004, page-cache warmth, ADPF-style hints S-011) but stock applies them generically or not at all.
- **FF-2 Jank.** FrameTimeline taxonomy (S-008): AppDeadlineMissed (app overrun; ART CC GC pauses are small ~2 ms but add up — S-006), BufferStuffing (backpressure), SurfaceFlinger CPU/GPU misses, DisplayHAL, PredictionError (vsync mis-prediction at refresh-rate switches — S-009). Amplifiers: direct-reclaim stalls (memory watermark behavior, S-017), background I/O storms, big.LITTLE mis-placement.
- **FF-3 Continuity loss.** lmkd kills by oom_score_adj under PSI thresholds (S-002/S-003); cached apps freeze ~10 s after caching (S-013); EMUI layered cleaners (App launch manager, Phone Manager, PowerGenie-class — S-010); standby buckets restrict after disuse (S-012); phantom process cap 32 (S-014). Result: avoidable reloads, lost resume quality.
- **FF-4 Sustained/predictability.** Thermal headroom exhaustion → freq clamps (S-005); stock response is reactive. Predictive shaping requires lead time (E-08).

### Root-cause thesis (PROPOSED — survived adversarial self-attack, DESIGN_SPACE §5)

Stock control is **reactive** (acts after stall/kill), **per-resource** (memory, CPU, thermal, I/O controllers uncoordinated), and **state-blind** (same policy at desk idle and mid-game). Damage concentrates at transitions and contention spikes. Therefore a **state-aware, predictive, coordinated, bounded** control plane — observing PSI-class early signals and lifecycle events, modeling state, and acting through whitelisted surfaces with admission control — can materially improve user-visible outcomes where static tweaks cannot.

## 2. User-visible goals (mission R-003)

| # | Goal | Metric family |
|---|---|---|
| G1 | Responsiveness | launch/switch latency p50/p90 |
| G2 | Smooth interaction, less stutter/jank/long pauses | FrameTimeline jank rate; p99 frame time; PSI stall time |
| G3 | Multitasking continuity | reload ratio in N-app cycle; resume latency |
| G4 | Fewer avoidable app reloads/exits | lmk/vendor kill counts on protected set; cold-start share of resumes |
| G5 | Better resume quality | activity display time after backgrounding; state-loss incidents |
| G6 | Sustained stability | metric drift over 30-min soak; throttle frequency/duration |
| G7 | Predictability under changing workloads | variance of p90 across workload phases |
| G8 | Intelligent management, not static tweaks | policy demonstrably state-dependent (region table), admission-gated |

## 3. Success model

Success is **only** user-visible outcome deltas vs stock on the same device (01 §8): SLO set defined in `../research/MEASUREMENT_STRATEGY.md`. Explicitly NOT success: free-RAM size, clock speed, synthetic benchmark scores, tweak counts. Minimum bars before any production claim: E-02 (launch), E-04 (continuity), E-10 (overhead <1% CPU), plus no battery regression (RSK-106 guard).

## 4. Non-goals

- HarmonyOS NEXT / 5 (not Android; out of scope — D-014).
- Kernel patches/modules in v1 (rejected: rollback/integrity cost; ARCH-C deferred — `../architecture/ALTERNATIVES.md`).
- Vendor-policy combat (silent counteraction of EMUI managers) — coexistence + user guidance instead (D-013).
- RAM "cleaning"/wholesale kills (rejected — `../research/REJECTED_IDEAS.md` RI-01).
- Thermal protection modification of any kind (invariant — `../architecture/SAFETY_INVARIANTS.md` INV-1).
- Benchmark/synthetic-metric optimization as an end.
- Account/banking/DRM/integrity/anti-tamper bypasses (hard ban).

## 5. Platform fact base (summary)

VERIFIED_FACT items F-006..F-015 with sources — canonical list in `TRUTH_REGISTER.md` §3; underlying sources in `.v10/working_memory/SOURCE_INDEX.md` (S-001..S-021). Device-specific extents remain HYPOTHESIS until E-series runs (D-015).

## 6. Assumptions and unknowns

- Assumption A1: root available on target when justified (mission-granted; degraded mode defined regardless).
- Assumption A2: userspace control surfaces suffice for meaningful SLO deltas — testable via E-02/E-04; if falsified, ARCH-C revisit permitted by D-007 REOPEN_IF.
- Critical unknowns U-003..U-007: `TRUTH_REGISTER.md` §4 and `.v10/state/PROJECT_STATE.json`.
