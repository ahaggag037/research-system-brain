# DESIGN SPACE (STATE B working document — scratch, not canonical docs)

Status: PROPOSED material. Will be distilled into docs/ at STATE C (R-054). Truth states per claim.

## 1. Problem decomposition (02 §1) — first principles

The nine target outcomes (R-003) decompose into four failure families:

**FF-1 Transition latency failures** (app launch, switch, resume).
Mechanism chain: process spawn → ART init → class/dex load → resource I/O → first layout/draw → first frame. Amplifiers: flash I/O latency; CPU frequency ramp latency (schedutil tick/rate limits); cross-cluster migration on big.LITTLE (capacity inversions); background contention during the episode; process not resident (see FF-3); compositor warm-up.
User sees: slow launch, long pause after tap, stale screenshot before real content on resume.

**FF-2 Steady-state interaction failures (jank/stutter).**
Mechanism chain: frame pipeline (Choreographer wakeup → input → animation → traversal → RenderThread → GPU → SurfaceFlinger → Display HAL) misses a vsync deadline. Causes partition into: (a) app-side work (GC pauses ~1–2 ms typical but compounding mid-frame, lock contention) — S-006; (b) CPU misadaptation (ramp latency, wrong cluster); (c) memory reclaim stalls on the allocation path (direct reclaim during frame work); (d) I/O stalls (fsync storms, page-cache misses on file-backed pages); (e) compositor/GPU deadline misses; (f) thermal throttle mid-episode.
Jank taxonomy is observable via FrameTimeline (AppDeadlineMissed, BufferStuffing, SfCpu/SfGpuDeadlineMissed, DisplayHAL, PredictionError) — S-008.

**FF-3 Continuity failures** (avoidable reloads/exits, poor resume).
Mechanism chain: app goes background → (cached) → freezer (Android 11+, cgroup v2 freezer, freeze ~10 s after cached, compaction on freeze incl. zram writeback — S-013) → memory pressure → lmkd kill by oom_score_adj under PSI thresholds (defaults: partial stall 70 ms, complete 700 ms — S-002/S-003) → next switch-in = cold/warm start. Vendor layer (EMUI App-launch management / Phone Manager cleanup / PowerGenie-class cleaners) adds aggressive kills beyond AOSP policy — S-010 (field evidence). Android 12+ phantom-process killer (32 child procs, excessive CPU) and standby buckets (jobs/alarms/FCM throttling; restricted bucket after 8 days on Android 13+) — S-012, S-014.

**FF-4 Sustained/predictability failures.**
Thermal saturation → reactive throttle cliff (Thermal API headroom is predictive — S-005 — but stock apps rarely use it); memory fragmentation/leak accumulation over uptime; background bursts (sync/index/update) colliding with foreground; multiple independent reactive controllers (lmkd, schedutil, thermal, vendor perfmgr) with different time constants → additive overreaction or oscillation.

**Cross-cutting root pattern (thesis):** user-visible damage concentrates at *transitions and contention spikes*. Stock control is reactive, threshold-based, per-resource, and uncoordinated; each controller sees a partial view and acts after the damage window opens. Usage is repetitive enough that prediction is feasible (S-015: APPM predicted next app + when with no prior training). Therefore: predictive, state-aware, coordinated, bounded intervention is the design space with real headroom — not static tweak values.

## 2. Product category answer (resolves R-005)

FixHUA is an **adaptive continuity control plane** for EMUI-class Android devices: an observation system, a state/prediction model, a coordination policy engine, a whitelist-bounded actuator set, and a safety governor — with a user-facing diagnosis/consent app. It is *not*: a booster/cleaner (symptom tweaks), a kernel patch, an app-side SDK, or an AI agent with arbitrary root. Category decision logged as D-006.

## 3. Concept families (02 §3 generate-first; dispositions per 02 §5/§8)

- CF-1 Observation plane (PSI triggers, /proc + dumpsys pollers, on-demand Perfetto, lifecycle map, kill/jank timeline) → PRODUCTION foundation, first build. Low risk, reveals device truth, falsifies assumptions.
- CF-2 Transition compute governance (cgroup/per-task uclamp min/max — S-004 — armed at launch/switch episodes; duration-capped) → PRODUCTION CANDIDATE after E-02.
- CF-3 Memory continuity governance → staged: (a) kill-pattern observation; (b) documented levers: device_config `max_cached_processes` (S-013), vm.swappiness/watermark/extra_free_kbytes (S-017, S-018), zram sizing policy; (c) timed oom_score_adj nudges (transient by nature — E-03 measures overwrite timing); (d) lmkd `ro.lmk.*` retune + lmkd restart → EXPERIMENTAL CANDIDATE only (protection gap during restart; RSK-102).
- CF-4 I/O coordination → PRODUCTION-LITE: ionice/ioprio deprioritization of background maintenance during interaction episodes; cgroup io.weight/io.latency shaping. Predictive page-cache prefetch of predicted-next-app's known launch file set (cf. TPP two-phase prefetch, S-016) → EXPERIMENTAL (memory cost on misprediction; bound size; idle-only arming; E-09 gates).
- CF-5 Thermal budgeting → PRODUCTION as ADVISORY SHAPING ONLY: reduce background concurrency/boost ceilings as headroom shrinks (S-005). Never write thermal trip points (hard invariant, R-040).
- CF-6 Next-app prediction (APPM-style Markov + time/context; no sensor requirement — S-015) → PRODUCTION as ARMER of low-regret actions only (protect-from-kill, small prefetch, boost arming); abstention when confidence below threshold (D-012).
- CF-7 Vendor-policy coexistence → PRODUCTION: detect EMUI power-management state via public surfaces; align or advise user once; never silently fight vendor policy (D-013).
- CF-8 Deep kernel modification (module/scheduler patch/custom kernel) → REJECTED for v1: violates hours-to-first-build, poor rollback, integrity risk; recorded as research-only.
- CF-9 Aggressive background "cleaning" to free RAM → REJECTED: directly increases FF-3 reloads; free RAM is not an outcome (R-045).
- CF-10 Forced max clocks / governor pinning → REJECTED production (thermal/battery hazard; symptom optimization); allowed only as lab diagnostic.
- CF-11 Global freezer/phantom-killer disable → REJECTED as default (breaks stock power model); scoped variant: detect pathological kills, suggest documented per-app exemptions with user consent.
- CF-12 zram algorithm/size retune → EXPERIMENTAL CANDIDATE (device-specific; disruptive reinit; E-06).

## 4. Architecture candidates (02 §9)

- ARCH-A "Profile pack" (static tuned values) — REJECTED: mission anti-goal (00 §0 "intelligent management rather than a static collection of generic tweaks").
- ARCH-B "Continuity control plane" — userspace control system: OBS → MODEL → POLICY → ACT with SAFE governor and UI. Leading candidate → refined to ARCH-FX-CP-01.
- ARCH-C Kernel module / deep kernel — REJECTED for v1 (rollback, integrity, hours-to-build); revisit only if userspace ceiling is proven with device evidence.
- ARCH-D App-only no-root — retained as DEGRADED MODE of ARCH-B (observation + app-scope mitigations), not the product: authority insufficient for FF-2/FF-3 mechanisms.
- ARCH-E ROM/AOSP modification — REJECTED: not installable on stock EMUI-class devices; contradicts installable-in-hours.

## 5. Adversarial self-attack on ARCH-FX-CP-01 (02 §10) + resolutions

1. *Optimizes symptom not cause* → Every metric in the success model is user-visible (jank rate, launch/resume latency, reload count); intermediate signals (PSI, uclamp) are levers, never goals (R-045).
2. *Telemetry overhead destroys gain* → PSI triggers are event-driven (S-007); pollers at ≤1 Hz; Perfetto only in diagnostic sessions; E-10 gates overhead budget (<1% avg CPU); adaptive sampling under load.
3. *Controllers fight (vendor perfmgr / power HAL also boost)* → Single-writer rule per control surface; detect vendor boosts; defer and fill gaps only; ownership map in component contracts.
4. *Feedback oscillation* → Hysteresis bands, minimum dwell times, action rate limits, integral-windup guards; control-theoretic grounding (S-019).
5. *Vendor policy overrides* → Coexistence (CF-7); capability detection at boot; graceful degradation matrix.
6. *Short-term gain, sustained regression* → Sustained-SLO tracking (30-min episodes); thermal guard; auto-degrade to stock on regression detection.
7. *Learning fits noise* → Predictor arms only low-regret actions; confidence gating; online evaluation with abstention; no irreversible learned action.
8. *Root infrastructure creates instability* → Privileged helper is small, schema-validated, no shell passthrough, no network; audit journal; fail-safe = stock; kill switch (RSK-107).
9. *Wrong abstraction* → Abstraction anchored to SLOs and failure families, not resources; FF model reviewed against device evidence at each experiment.
10. *Battery regression* → Energy budget as constraint in policy objective; monitor via BatteryStats/HAL counters; degrade policy if budget breached.

## 6. Convergence assessment (02 §11)

- Core problem model stable: yes — four failure families + reactive-vs-predictive thesis; rounds 1–3 research refined but did not overturn it.
- Major alternatives seriously considered: ARCH-A/C/D/E + CF-8..CF-12 dispositions recorded.
- Survived adversarial attack: 10 attacks → mitigations embedded in architecture (SAFE, single-writer, dwell, abstention).
- Recent research refines rather than overturns: yes (round 3 mostly added mechanism detail).
- Critical unknowns explicit: U-003 (device-specific control writability), U-004 (budgets), U-005 (EMUI surface variance), U-006 (thermal headroom predictability on target class), U-007 (predictor accuracy on real usage).
- Implementation-blocking unknowns have concrete tests: E-01..E-12 defined.
- First useful build clear: FB-1 observer, zero actuation, hours-scale.
- Risky ideas dispositioned: CF catalog above + RISK_TRANSMUTATION (STATE C).
- End-to-end coherent: OBS→MODEL→POLICY→ACT under SAFE, UI consent, STORE state — yes.

Conclusion: convergence criteria met → write DESIGN_CONVERGED, enter STATE C.
