# EXPERIMENT REGISTRY

## Experiment template

EXPERIMENT_ID:
STATUS: PROPOSED | SHADOW | READY | RUN | PASSED | FAILED | INCONCLUSIVE | ABORTED
HYPOTHESIS:
WHY_IT_MATTERS:
INPUT/CONDITIONS:
MEASUREMENTS:
EXPECTED_RESULT:
FALSIFICATION_CONDITION:
ABORT_CONDITION:
RISK_LEVEL:
RESULT:
DECISION_IMPACT:
ARTIFACTS:

---

Environment note (truth-state discipline, R-046): **no physical target device exists in this environment.** All experiments below are device-validation plans. None has been RUN. Device-dependent claims remain HYPOTHESIS/UNKNOWN until these execute on device (05A §13: package reports BLOCKED_BY_DEVICE_EVIDENCE where applicable). Smallest-discovery-step definitions satisfy 05 §7.

## E-01 — PSI trigger fidelity as early-warning
STATUS: READY (device needed)
HYPOTHESIS: PSI `some/full` memory triggers fire early enough to act before user-visible stall during pressure onset.
WHY_IT_MATTERS: OBS foundation; gates all pressure-driven policy.
INPUT/CONDITIONS: Rooted test device; register triggers (e.g., some 70 ms/1 s, full 700 ms/1 s memory; io/cpu analogs); controlled allocator app induces known pressure ramp.
MEASUREMENTS: Trigger timestamp vs /proc/vmstat allocstall deltas vs Perfetto mm events; event latency; false rate at idle.
EXPECTED_RESULT: Trigger precedes allocstall spike by ≥100 ms at moderate ramp.
FALSIFICATION_CONDITION: Triggers arrive after user-visible stall onset or unstable at idle.
ABORT_CONDITION: System instability during pressure induction.
RISK_LEVEL: Low (read-only monitoring).
RESULT: —
DECISION_IMPACT: If FAILED → fall back to averaged PSI polling (2 s) + vmstat deltas; POLICY reaction times lengthen.
ARTIFACTS: bench script + trace (device run).

## E-02 — uclamp transition boost effect on launch latency
STATUS: READY (device needed)
HYPOTHESIS: Raising cpu.uclamp.min (bounded, e.g., +20–30% for ≤2 s) at app-launch/switch reduces cold/warm start latency measurably.
WHY_IT_MATTERS: CF-2 production admission (01 §3).
INPUT/CONDITIONS: Rooted device; A/B same app set, cleared caches; boost applied via cgroup write at launch detection.
MEASUREMENTS: `am start -W` TotalTime/WaitTime; Perfetto frame timeline to first frame; schedutil freq trace; energy delta via BatteryStats. Interpretation note (S-026): on kernels ≥6.6 (android15+) the schedutil rework honors uclamp hints more faithfully — record kernel train per run; effect sizes are train-specific.
EXPECTED_RESULT: ≥10% median launch latency reduction on mid-tier SoC, no sustained freq overshoot after cap expiry.
FALSIFICATION_CONDITION: <5% change or regression from cluster migration churn.
ABORT_CONDITION: Thermal warning; instability.
RISK_LEVEL: Low-medium (bounded writes to documented cgroup files; rollback = restore prior value).
RESULT: —
DECISION_IMPACT: PASS → CF-2 production candidate; FAIL → rely on vendor boost coexistence only.
ARTIFACTS: A/B dataset.

## E-03 — oom_score_adj overwrite behavior
STATUS: READY (device needed)
HYPOTHESIS: Externally written oom_score_adj on cached apps persists long enough to be useful OR is overwritten at predictable AMS events.
WHY_IT_MATTERS: Determines whether CF-3(c) timed nudges are viable or only documented levers (device_config) are safe.
INPUT/CONDITIONS: Rooted; set adj on N cached apps; lifecycle event log.
MEASUREMENTS: Time-to-overwrite distribution; overwrite triggers (task switch, trim, freeze).
EXPECTED_RESULT: Overwrite only at AMS recompute events; nudge effective between events.
FALSIFICATION_CONDITION: Immediate/random overwrite making nudges noise.
ABORT_CONDITION: lmkd misbehavior observed (unexpected kills).
RISK_LEVEL: Medium (kill-order perturbation; bench device only).
RESULT: —
DECISION_IMPACT: PASS → timed-nudge component; FAIL → drop CF-3(c), rely on policy-level levers.
ARTIFACTS: overwrite log.

## E-04 — max_cached_processes continuity effect
STATUS: READY (device needed)
HYPOTHESIS: Raising `activity_manager max_cached_processes` (documented device_config override, S-013) within memory budget reduces avoidable reloads without pressure regressions.
WHY_IT_MATTERS: Cheapest documented continuity lever.
INPUT/CONDITIONS: Fixed multitasking script (cycle 12 apps), values {default, +8, +16}; memory headroom monitored.
MEASUREMENTS: Cold/warm resume ratio; PSI memory stall; lmk kill count; jank rate during script.
EXPECTED_RESULT: Reload count ↓ ≥25% at +8 with PSI stall unchanged.
FALSIFICATION_CONDITION: PSI stall or kills increase (pressure shift).
ABORT_CONDITION: PSI full stalls sustained >1 s/window.
RISK_LEVEL: Low (documented runtime override; revert via device_config delete).
RESULT: —
DECISION_IMPACT: PASS → default-on policy with memory-budget guard; FAIL → keep stock.
ARTIFACTS: script + metrics.

## E-05 — Freezer resume cost
STATUS: READY (device needed)
HYPOTHESIS: Frozen cached apps resume slower than non-frozen cached (freeze compaction + unfreeze cost), but cheaper than cold start.
WHY_IT_MATTERS: Quantifies freezer vs kill trade; informs protect-list policy.
INPUT/CONDITIONS: Device with use_freezer on; measured resumes after 30 s/5 min/30 min background.
MEASUREMENTS: Resume latency (activity display time); unfreeze latency — on Android 16+ (API 36) prefer official `IBinder.FrozenStateChangeCallback` (S-025, capability-gated on kernel binder support), fallback = infer from binder FrozenStateChange trace events; compare freezer off (device_config toggle, documented).
EXPECTED_RESULT: Frozen resume ≥ non-frozen cached, ≪ cold start.
FALSIFICATION_CONDITION: Frozen ≈ cold start (freezer loses its point) → stronger keep-warm policy justified.
ABORT_CONDITION: —
RISK_LEVEL: Low.
RESULT: —
DECISION_IMPACT: Calibrates keep-warm list size and freeze-window policy.
ARTIFACTS: resume latency table.

## E-06 — Memory tunables under pressure (swappiness, watermarks, zram)
STATUS: READY (device needed)
HYPOTHESIS: State-aware vm tuning (swappiness toward 100–180 when zram is fast — S-018; extra_free_kbytes/watermark_scale_factor within kernel-documented ranges) reduces direct-reclaim stalls during interaction under pressure.
WHY_IT_MATTERS: FF-2(c)/FF-3 mitigation; validates "no magic values" operating regions (02 §6).
INPUT/CONDITIONS: Rooted; pressure workload (background alloc + foreground scroll benchmark); per-setting runs. **Record reclaim-engine state per run (D-016): `/sys/kernel/mm/lru_gen/enabled` + `min_ttl_ms`; regions calibrated separately for classic LRU vs MGLRU kernels.**
MEASUREMENTS: allocstall/duration, PSI some/full, jank rate, zram stats (compress ratio, reads), battery; MGLRU generation stats (`/sys/kernel/mm/lru_gen/`) when present.
EXPECTED_RESULT: Measurable allocstall ↓ at fixed workload vs stock.
FALSIFICATION_CONDITION: No effect or regression (thrash).
ABORT_CONDITION: UI freezes >2 s; watchdog trip.
RISK_LEVEL: Medium (system-wide VM behavior; bench only; rollback = restore defaults).
RESULT: —
DECISION_IMPACT: Establishes per-state VM operating regions for POLICY.
ARTIFACTS: region table.

## E-07 — Background I/O deprioritization during interaction
STATUS: READY (device needed)
HYPOTHESIS: ionice (best-effort/idle) + cgroup io weight reduction for known background maintenance processes during foreground episodes reduces jank spikes from sync/index/update I/O.
WHY_IT_MATTERS: FF-2(d) mitigation with narrow blast radius.
INPUT/CONDITIONS: Controlled background I/O generator + scroll/launch foreground workload.
MEASUREMENTS: FrameTimeline jank (S-008), io PSI, foreground p99 frame time.
EXPECTED_RESULT: Jank spikes during background storm ↓ measurably.
FALSIFICATION_CONDITION: No change (I/O not bottleneck) or background starvation side-effects.
ABORT_CONDITION: Background app data corruption reports (none expected — scheduling only).
RISK_LEVEL: Low.
RESULT: —
DECISION_IMPACT: PASS → CF-4 production-lite arm.
ARTIFACTS: traces.

## E-08 — Thermal headroom predictability
STATUS: READY (device needed)
HYPOTHESIS: Thermal zone trends provide a usable predictive window (≥10 s) before throttle events on the target class.
WHY_IT_MATTERS: CF-5 viability (advisory shaping needs lead time).
INPUT/CONDITIONS: Sustained GPU/CPU load script; thermal zones + getThermalHeadroom polling at 1 Hz; skin temperature proxy if available.
MEASUREMENTS: Time from threshold-crossing signal to first freq clamp; variance across runs/ambient.
EXPECTED_RESULT: Consistent ≥10 s lead.
FALSIFICATION_CONDITION: Lead time <3 s or erratic → CF-5 downgraded to reactive smoothing.
ABORT_CONDITION: Device thermal warning (stop load script).
RISK_LEVEL: Low-medium (sustained load; supervised; no thermal config writes — hard invariant).
RESULT: —
DECISION_IMPACT: Sets CF-5 horizon parameters.
ARTIFACTS: thermal traces.

## E-09 — Next-app prediction accuracy
STATUS: PROPOSED (needs usage data collection via FB-1)
HYPOTHESIS: Lightweight Markov + time-of-day/context features achieve Accuracy@3 sufficient that arming low-regret actions has positive expected value.
WHY_IT_MATTERS: CF-6 admission; prevents learning-on-noise (attack #7).
INPUT/CONDITIONS: ≥2 weeks UsageStats/app-usage events from FB-1 observers (opt-in); offline training.
MEASUREMENTS: Accuracy@1/@3, per-user abstention curve, expected regret of armed actions at thresholds.
EXPECTED_RESULT: Accuracy@3 ≥ 60% with abstention keeping armed-action regret < small bound.
FALSIFICATION_CONDITION: Accuracy insufficient even @3 with abstention → CF-6 reduced to recency/frequency heuristics only.
ABORT_CONDITION: —
RISK_LEVEL: None (offline analysis; privacy: on-device data only).
RESULT: —
DECISION_IMPACT: Arms or retires predictive layer.
ARTIFACTS: model eval report.

## E-10 — Telemetry overhead budget
STATUS: READY (device needed)
HYPOTHESIS: OBS production telemetry (PSI triggers + ≤1 Hz pollers + lifecycle events, no Perfetto) costs <1% average CPU and negligible battery.
WHY_IT_MATTERS: Attack #2 (overhead destroys gain); production admission of OBS.
INPUT/CONDITIONS: FB-1 build; 24 h normal-use run; comparison idle baseline.
MEASUREMENTS: CPU time of OBS processes; battery drain attribution; wakelock count (must be ~0).
EXPECTED_RESULT: <1% CPU avg; no measurable battery delta.
FALSIFICATION_CONDITION: Overhead above budget → reduce rates, drop counters, batch wakes.
ABORT_CONDITION: —
RISK_LEVEL: Low.
RESULT: —
DECISION_IMPACT: Locks production sampling rates.
ARTIFACTS: overhead report.

## E-11 — lmkd property retune canary (EXPERIMENTAL gate)
STATUS: PROPOSED (requires E-01..E-06 evidence first)
HYPOTHESIS: Adjusting `ro.lmk.*` thresholds (e.g., kill_heaviest_task, lowmem_min_oom_score bounds, PSI stall thresholds) with lmkd restart yields better continuity/stability trade than stock on target class.
WHY_IT_MATTERS: Deepest continuity lever; also highest policy risk (kill policy).
INPUT/CONDITIONS: Bench device, root, full rollback script ready; supervision. Read effective per-device defaults first (S-023: version drift exists, e.g. kill_timeout_ms 0→100 ms on master) — deltas are computed against observed stock, never assumed defaults.
MEASUREMENTS: Kill distribution vs pressure; protection gap during restart; stability over soak; resume metrics.
EXPECTED_RESULT: Fewer foreground-adjacent kills at equal stability.
FALSIFICATION_CONDITION: Any unexplained instability or gap-window kills.
ABORT_CONDITION: Any system_server instability → immediate stock restore.
RISK_LEVEL: HIGH — gated behind E-01..E-06; canary-only; never default.
RESULT: —
DECISION_IMPACT: Dispositions CF-3(d) production vs research-only.
ARTIFACTS: canary report.

## E-12 — EMUI power-management state detection
STATUS: READY (device needed)
HYPOTHESIS: Public APIs/settings state (PowerManager.isIgnoringBatteryOptimizations, UsageStats bucket, App-launch management state where readable) suffice to detect vendor kill-policy exposure per app.
WHY_IT_MATTERS: CF-7 coexistence and truthful UI guidance.
INPUT/CONDITIONS: EMUI device; enumerate surfaces; compare against manual settings screens.
MEASUREMENTS: Detection coverage vs manual audit.
EXPECTED_RESULT: ≥80% of relevant per-app states detectable programmatically.
FALSIFICATION_CONDITION: Key states unreadable → UI falls back to guided manual checklist (S-010 pattern).
ABORT_CONDITION: —
RISK_LEVEL: Low (read-only).
RESULT: —
DECISION_IMPACT: Sets CF-7 automation depth.
ARTIFACTS: capability matrix per EMUI version.
