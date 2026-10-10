# RISK TRANSMUTATION CATALOG — FixHUA

> 05 §9: preserve unconventional high-impact ideas with full transmutation records (raw idea → expected benefit → why risky → hazard mechanism → model/reasoning → redesigned bounded version → observations required → uncertainty → abort conditions → rollback → shortest falsification experiment → current disposition). Risk rows live in `.v10/working_memory/RISK_REGISTER.md`.

## RT-01 — Direct oom_score_adj management (kill-order override)

- **Raw idea:** continuously rewrite `oom_score_adj` so valued background apps survive pressure.
- **Expected benefit:** fewer continuity-breaking kills (FF-3).
- **Why risky:** fights AMS/OomAdjuster, which recomputes adj on lifecycle events; wrong adj ordering can push kills toward important processes.
- **Hazard mechanism:** overwrite race (RSK-103); policy oscillation between AMS and FixHUA.
- **Model/reasoning:** AMS recompute events are the natural decision points; between them adj is stable; nudges timed at events ride the platform instead of fighting it (D-013 spirit).
- **Redesigned bounded version:** CS-2 = *timed, bounded* nudges on a small predicted-return set, applied at lifecycle events, tolerating overwrite; never below foreground-adjacent thresholds; journal every write.
- **Observations required:** overwrite timing distribution (E-03); kill pattern shifts.
- **Uncertainty:** HIGH until E-03 (overwrite timing unknown — U-003/U-005).
- **Abort conditions:** unexpected kill pattern attributable to nudges.
- **Rollback:** stop nudging; AMS recomputes stock adj automatically.
- **Shortest falsification experiment:** E-03.
- **Disposition:** PRODUCTION CANDIDATE gated by E-03 (else dropped to CS-3/CS-4 policy-level levers only).

## RT-02 — Predictive page-cache warming (prefetch next app)

- **Raw idea:** pre-load the predicted next app's files into page cache before the user taps (APPM/TPP lineage — S-015/S-016).
- **Expected benefit:** faster cold/warm starts (FF-1) at the moment it matters.
- **Why risky:** misprediction wastes memory bandwidth and RAM; wrong prefetch under pressure can *cause* evictions that hurt the current app (TPP's documented PBAL overhead).
- **Hazard mechanism:** unbounded prefetch + wrong candidate + concurrent pressure = self-inflicted contention.
- **Model/reasoning:** regret is bounded if prefetch is (a) size-bounded, (b) idle-only, (c) armed only above calibrated confidence, (d) reversible (page cache is evictable by design).
- **Redesigned bounded version:** CS-7 = bounded page-cache warm (cap MB), armed only from the low-regret set (D-012), only when pressure_regime=calm and device idle-ish; online regret accounting disables arming above bound (INV-12).
- **Observations required:** prediction accuracy (E-09); memory pressure deltas during warming.
- **Uncertainty:** MEDIUM-HIGH until E-09 on real usage (U-007).
- **Abort conditions:** measured regret above bound; pressure regressions during warm windows.
- **Rollback:** disable arming (config); warmed pages evict naturally.
- **Shortest falsification experiment:** E-09 (offline on FB-1 data, zero device risk).
- **Disposition:** PRODUCTION CANDIDATE gated by E-09; heuristic (recency) fallback defined.

## RT-03 — lmkd policy retune (`ro.lmk.*` + restart)

- **Raw idea:** retune lmkd PSI thresholds/kill policy (e.g., kill_heaviest_task, lowmem_min_oom_score) for continuity/stability trade better than stock.
- **Expected benefit:** direct control of the kill policy at the root of FF-3.
- **Why risky:** `ro.*` changes need lmkd restart → protection gap window (RSK-102); wrong thresholds destabilize the whole device; vendor-locked properties may not apply.
- **Hazard mechanism:** gap-window OOM/wrong-kill; persistent bad kill policy.
- **Model/reasoning:** the hazard is concentrated in the restart window and in unbounded threshold changes; both are containable on a supervised bench with instant restore.
- **Redesigned bounded version:** CS-9 EXPERIMENTAL-only canary: single-parameter deltas, pre/post health checks, instant stock restore script armed, soak-gated.
- **Observations required:** gap window length; kill distribution vs pressure; stability soak.
- **Uncertainty:** HIGH; property mutability per device UNKNOWN (U-003).
- **Abort conditions:** any system_server instability or unexplained kill → instant stock restore.
- **Rollback:** stock property restore + lmkd restart; reboot option.
- **Shortest falsification experiment:** E-11 (canary), gated behind E-01..E-06.
- **Disposition:** RESEARCH-ONLY by default; EXPERIMENTAL gate defined; never ships in release without full pipeline evidence.

## RT-04 — Thermal-adaptive pre-shaping (act before the throttle)

- **Raw idea:** use thermal trends to shed/shape load *before* the platform clamps frequencies.
- **Expected benefit:** smoother sustained sessions (FF-4); less abrupt throttling.
- **Why risky:** naive versions either touch thermal config (FORBIDDEN — INV-1) or degrade performance earlier than necessary (net loss).
- **Hazard mechanism:** over-eager shaping = self-throttle; under-eager = no effect; signal noise → oscillation.
- **Model/reasoning:** value exists only with prediction lead time; without ≥10 s lead the rational policy degenerates to reactive smoothing (E-08 decides which world we are in).
- **Redesigned bounded version:** CS-8 advisory-only: FixHUA relaxes *its own* boosts/warming and deprioritizes admitted background I/O; platform thermal stack untouched.
- **Observations required:** lead-time distribution across loads/ambient (E-08).
- **Uncertainty:** MEDIUM (U-006).
- **Abort conditions:** lead time <3 s or erratic → demote to reactive smoothing.
- **Rollback:** none needed (advisory); disable CS-8.
- **Shortest falsification experiment:** E-08.
- **Disposition:** PRODUCTION CANDIDATE gated by E-08.

## RT-05 — Raising cached-process ceiling (memory-pressure trade)

- **Raw idea:** raise `max_cached_processes` so more apps survive backgrounded (documented device_config override — S-013).
- **Expected benefit:** fewer reloads (FF-3) at zero code risk to processes themselves.
- **Why risky:** more cached processes = less free memory = more pressure → could *increase* kills/jank (the classic booster fallacy inverted).
- **Hazard mechanism:** pressure displacement: continuity win paid for with FF-2 jank or foreground-adjacent kills.
- **Model/reasoning:** bounded raises within measured memory budget shift the trade point deliberately; PSI co-monitoring detects the regression side immediately.
- **Redesigned bounded version:** CS-3 = modest stepwise raises (+8 first) with PSI regression guard and automatic reversal (E-04 protocol bakes the guard in).
- **Observations required:** PSI stall deltas; kill count shifts; reload ratio.
- **Uncertainty:** MEDIUM (U-004).
- **Abort conditions:** PSI full stalls sustained >1 s/window during E-04.
- **Rollback:** device_config delete (documented).
- **Shortest falsification experiment:** E-04.
- **Disposition:** PRODUCTION CANDIDATE gated by E-04.
