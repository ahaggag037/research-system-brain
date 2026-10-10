# SOURCE INDEX

## Source template

SOURCE_ID:
TYPE: DEVICE_OBSERVATION | PRIMARY_PLATFORM | VENDOR | SOURCE_CODE | ACADEMIC | TECHNICAL | COMMUNITY | OTHER
TITLE:
LOCATION:
DATE/RECENCY:
CLAIMS_SUPPORTED:
LIMITATIONS:
NOTES:

---

## S-001
TYPE: OTHER (mission contract)
TITLE: FixHUA Blind Genesis V10.3 Clean Repository Bound mission package
LOCATION: user-supplied ZIP; canonical snapshot at `mission/` in this repo
DATE/RECENCY: V10.3, received 2026-10-10
CLAIMS_SUPPORTED: All R-xxx requirement sources; mission constraints; state machine.
LIMITATIONS: Defines the mission, not the problem domain facts.
NOTES: SHA256SUMS verified OK at intake. `_human/` excluded and unread (D-002, R-010).

## S-002
TYPE: PRIMARY_PLATFORM
TITLE: kswapd and lmkd interaction — LMKD tuning properties
LOCATION: https://developer.android.com/topic/performance/memory/guide/kswapd-lmkd-interaction
DATE/RECENCY: updated 2026-08-25 (fetched 2026-10-10)
CLAIMS_SUPPORTED: lmkd tunable via `ro.lmk.*` properties (use_psi, psi_partial_stall_ms default 70 high-end / 200 low-RAM, psi_complete_stall_ms 700, kill_heaviest_task, direct_reclaim_threshold_ms, swap_compression_ratio, lowmem_min_oom_score); Perfetto config for lmk/kswapd/PSI observation.
LIMITATIONS: Pixel-class defaults; OEM values vary. `ro.*` runtime mutability not implied (see E-11).
NOTES: Authority A.

## S-003
TYPE: SOURCE_CODE
TITLE: platform/system/memory/lmkd (AOSP)
LOCATION: https://android.googlesource.com/platform/system/memory/lmkd/
CLAIMS_SUPPORTED: Full `ro.lmk.*` property list incl. pressure_after_kill_min_score, lowmem_min_oom_score default 701 semantics.
LIMITATIONS: —
NOTES: Authority A.

## S-004
TYPE: PRIMARY_PLATFORM (kernel docs)
TITLE: Utilization Clamping (sched-util-clamp)
LOCATION: https://docs.kernel.org/scheduler/sched-util-clamp.html
CLAIMS_SUPPORTED: uclamp per-task via sched_setattr; cgroup v2 `cpu.uclamp.min/max` (min=protection max-of-hierarchy, max=limit min-of-hierarchy); range 0–1024; affects freq selection (schedutil) and big.LITTLE placement.
LIMITATIONS: Vendor kernels may restrict cgroup writes; Android cgroup hierarchy managed by libprocessgroup → external writes may be overwritten (drives E-03-style ownership checks).
NOTES: Authority A.

## S-005
TYPE: PRIMARY_PLATFORM
TITLE: Thermal API (ADPF)
LOCATION: https://developer.android.com/games/optimize/adpf/thermal
DATE/RECENCY: 2026-02-26 (fetched 2026-10-10)
CLAIMS_SUPPORTED: `getThermalHeadroom` predicts time-to-overheat at current performance; proactive workload reduction advised before throttle.
LIMITATIONS: App-scoped API; system-wide headroom from thermal zones is device-specific (U-006, E-08).
NOTES: Authority A.

## S-006
TYPE: PRIMARY_PLATFORM
TITLE: Debug ART garbage collection
LOCATION: https://source.android.com/docs/core/runtime/gc-debug
DATE/RECENCY: 2024-08-26
CLAIMS_SUPPORTED: ART default Concurrent Copying GC since Android 8; generational since 10; one small pause, roughly constant w.r.t. heap size; typical young-CC pause ~1.8 ms avg example.
LIMITATIONS: App-side cause; system-level mitigation is indirect (CPU/I-O readiness), not GC tuning of other apps.
NOTES: Authority S.

## S-007
TYPE: PRIMARY_PLATFORM (kernel docs)
TITLE: PSI — Pressure Stall Information
LOCATION: https://docs.kernel.org/accounting/psi.html
CLAIMS_SUPPORTED: poll()-able triggers `<some|full> <stall_us> <window_us>` on /proc/pressure/{cpu,io,memory} and per-cgroup pressure files; window 500 ms–10 s; notification rate-limited to one per window; unprivileged monitors need 2 s-multiple windows.
LIMITATIONS: Requires kernel ≥5.2 for triggers (Android GKI kernels qualify; per-device verify — E-01).
NOTES: Authority A.

## S-008
TYPE: TECHNICAL (Perfetto docs)
TITLE: Android Jank detection with FrameTimeline
LOCATION: https://perfetto.dev/docs/data-sources/frametimeline
CLAIMS_SUPPORTED: Jank taxonomy (AppDeadlineMissed, BufferStuffing, SurfaceFlingerCpu/GpuDeadlineMissed, DisplayHAL, PredictionError) and measurement semantics.
LIMITATIONS: —
NOTES: Basis for FixHUA jank SLO measurement.

## S-009
TYPE: TECHNICAL (Android Developers Blog)
TITLE: High refresh rate rendering on Android
LOCATION: https://android-developers.googleblog.com/2020/04/high-refresh-rate-rendering-on-android.html
DATE/RECENCY: 2020-04-27
CLAIMS_SUPPORTED: Choreographer vsync offsets (app wakeup +2 ms, SF +6 ms at 60 Hz), render-ahead at 90 Hz; frame budget mechanics.
LIMITATIONS: Pixel 4-era numbers; concept stable.
NOTES: Authority A.

## S-010
TYPE: VENDOR (public support doc) + COMMUNITY field evidence
TITLE: Huawei EMUI background protection settings; EMUI power-management kill behavior field reports
LOCATION: https://consumer.huawei.com/en/support/content/en-us15850065/ ; https://support.equilab.horse/support/solutions/articles/154000029887-huawei-interrupted-trainings
CLAIMS_SUPPORTED: EMUI has layered background control: Phone Manager App-launch (auto/secondary/background toggles), battery optimization, recents lock, cleanup mechanisms; PowerGenie-class cleaners reported on EMUI 8–10; multiple stacked mechanisms kill background apps by default policy.
LIMITATIONS: Community reports = leads/field evidence, not authoritative mechanism docs; EMUI version variance high (U-005).
NOTES: Authority A (Huawei support), NA (field guide). Drives CF-7 coexistence and continuity policy.

## S-011
TYPE: PRIMARY_PLATFORM
TITLE: Performance Hint Manager (NDK reference)
LOCATION: https://developer.android.com/ndk/reference/group/a-performance-hint
DATE/RECENCY: 2026-08-27 (fetched 2026-10-10)
CLAIMS_SUPPORTED: ADPF hint sessions exist: create session over thread set with target work duration; reportActualWorkDuration; updateTargetWorkDuration; notifyWorkloadIncrease/Spike/Reset (API 36); graphics-pipeline auto-timing mode; rate-limited hints; system adjusts scheduling/performance toward target.
LIMITATIONS: Designed for apps' own threads; system-wide use requires privileged cooperation, not app API abuse.
NOTES: Authority A. Evidence that the platform itself validates the hint-based adaptive model FixHUA generalizes.

## S-012
TYPE: PRIMARY_PLATFORM
TITLE: App Standby Buckets
LOCATION: https://developer.android.com/topic/performance/appstandby
DATE/RECENCY: 2026-10-01 (fetched 2026-10-10)
CLAIMS_SUPPORTED: Bucket system (active/working/frequent/rare/restricted); restricted bucket (Android 12+) limits jobs to daily 10-min batch, one alarm/day; Android 13 restricts after 8 days non-interaction; restrictions only on battery.
LIMITATIONS: —
NOTES: Authority A.

## S-013
TYPE: PRIMARY_PLATFORM
TITLE: Cached apps freezer
LOCATION: https://source.android.com/docs/core/perf/cached-apps-freezer
DATE/RECENCY: 2026-09-29 (fetched 2026-10-10)
CLAIMS_SUPPORTED: Android 11+ cgroup v2 freezer for cached apps; freeze ~10 s after cached (Android 14+); immediate unfreeze on lifecycle events; on-freeze compaction incl. dirty-page writeback + zram swap; frozen apps can't GC/trim; TCP sockets terminated when all app procs frozen; `use_freezer` device_config toggle; `max_cached_processes` runtime override via device_config (e.g. 1024 test).
LIMITATIONS: Vendor may customize timing.
NOTES: Authority S. Directly powers CF-3 levers.

## S-014
TYPE: TECHNICAL (community deep-dive, well-sourced)
TITLE: Phantom, Cached and Empty Processes (agnostic-apollo Android-Docs)
LOCATION: https://github.com/agnostic-apollo/Android-Docs/blob/master/en/docs/apps/processes/phantom-cached-and-empty-processes.md
CLAIMS_SUPPORTED: Android 12+ phantom process killer: >32 child processes (all apps combined) or excessive background CPU → kill; Android 14+ toggle `settings_enable_monitor_phantom_procs` / Developer option "Disable child process restrictions"; AOSP commit references 15755084/5706277f.
LIMITATIONS: Community documentation (labeled as such per 02 §2); corroborated by AOSP commit IDs.
NOTES: Authority S-rated community repo. Basis for CF-11 scoped variant.

## S-015
TYPE: ACADEMIC
TITLE: Practical Prediction and Prefetch for Faster Access to Applications on Mobile Phones (APPM; Böhmer, Chu, Ganesan)
LOCATION: https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/ubi1443-parate.pdf
CLAIMS_SUPPORTED: Next-app + when prediction feasible without prior training or extra sensors; parallel prefetch on unlock practical within mobile OS constraints.
LIMITATIONS: 2012-era devices; method (not numbers) transfers.
NOTES: Authority S (Microsoft Research publication). Foundation for CF-6.

## S-016
TYPE: ACADEMIC
TITLE: Accelerate Application Launch via Two-Phase Prefetching (TPP), DATE 2023
LOCATION: https://past.date-conference.com/proceedings-archive/2023/DATA/621.pdf
CLAIMS_SUPPORTED: Two-phase launch prefetch (predictive pre-launch of key pages + sliding-window during launch) reduces app launch latency; launch I/O is largely invariant; PBAL memory overhead risk on misprediction documented (bounded prefetch rationale).
LIMITATIONS: Kernel-space implementation; FixHUA uses userspace page-cache warming instead.
NOTES: Supports CF-4 experimental arm + bounds.

## S-017
TYPE: SOURCE_CODE (Android common kernel commit)
TITLE: add extra free kbytes tunable
LOCATION: https://android.googlesource.com/kernel/common.git/+/92189d47f66c67e5fd92eafaa287e153197a454f%5E%21/
CLAIMS_SUPPORTED: vm.extra_free_kbytes raises min/low watermark gap to keep burst allocations out of direct reclaim; explicitly revived for Android kernels.
LIMITATIONS: Deprecated "on kernels that do better at avoiding direct reclaim" — per-device check (E-06).
NOTES: Authority A. Mechanism for FF-2(c) mitigation.

## S-018
TYPE: TECHNICAL (kernel-doc-grounded practice guide)
TITLE: Zram, zswap and Linux Swap: A Practical Configuration Guide
LOCATION: https://www.enricopesce.it/zram-zswap-linux-swap-configuration/
DATE/RECENCY: 2026-07-17
CLAIMS_SUPPORTED: vm.swappiness semantics = relative cost of swap vs file paging on 0–200 scale; >100 values rational for fast in-memory compressed swap; swappiness=0 does not disable swap, only defers it to worst moment.
LIMITATIONS: Community/technical press; semantics cross-checked against kernel docs.
NOTES: Kills the "lower swappiness is always better" folklore — state-aware policy per 02 §6.

## S-019
TYPE: ACADEMIC (book)
TITLE: Feedback Control of Computing Systems (Hellerstein, Diao, Parekh, Tilbury)
LOCATION: https://www.academia.edu/43854559/ (record); Wiley/IEEE publication
CLAIMS_SUPPORTED: Formal grounding for applying feedback control (modeling, PI control, stability, settling) to computing systems — used for POLICY design discipline (hysteresis, dwell, windup guards).
LIMITATIONS: General theory, not mobile-specific.
NOTES: Authority A.

## S-020
TYPE: TECHNICAL (reference)
TITLE: HarmonyOS 5 / NEXT overview
LOCATION: https://en.wikipedia.org/wiki/HarmonyOS_5
CLAIMS_SUPPORTED: HarmonyOS NEXT/5 has no AOSP core, cannot run Android apps, uses HongMeng kernel; EMUI and HarmonyOS ≤4 are Android-based. Bounds "Huawei-class Android phone" to Android-based Huawei devices (EMUI / dual-framework HarmonyOS ≤4).
LIMITATIONS: Tertiary reference; consistent with Huawei developer announcements.
NOTES: Authority A. Drives D-014 target bounding.

## S-021
TYPE: PRIMARY_PLATFORM
TITLE: Cached apps freezer / system tracing overviews (Perfetto)
LOCATION: https://developer.android.com/topic/performance/tracing ; https://perfetto.dev/docs/concepts/config
DATE/RECENCY: 2026-10-01 (fetched 2026-10-10)
CLAIMS_SUPPORTED: Perfetto = platform tracing since Android 10; cmdline + programmable TraceConfig; data sources (ftrace, process_stats, counters) suitable for OBS diagnostic sessions.
LIMITATIONS: Always-on Perfetto is too heavy for production telemetry → diagnostic mode only (attack #2 resolution).
NOTES: Authority A.

---

## STATE D research-expansion round (CP-004 → CP-005)

Mandatory challenge pass on consequential claims (handoff §8). Format: QUESTION → SOURCE → WHAT WAS LEARNED → CONFIDENCE → DECISION/ARCHITECTURE IMPACT. New sources S-022..S-026 below; decision impact recorded in DECISION_LOG D-016; new facts F-016..F-019 in TRUTH_REGISTER.

### Q-R1 — Does the reclaim engine on modern targets still behave as S-017/S-018 (classic kswapd/LRU) assume? (kernel-version-sensitive; could change CS-5/E-06)
→ SOURCE: S-022 (kernel MGLRU docs; Android enablement reporting; ACK branches)
→ LEARNED: MGLRU merged in Linux 6.1, backported to android13-5.10/5.15 ACK, and **enabled by default on all android14-* GKI kernels** (android14-5.15/6.1, hence android15-6.6, android16-6.12 devices). Runtime state visible at `/sys/kernel/mm/lru_gen/enabled` (bitmask) + `min_ttl_ms`; build flags `CONFIG_LRU_GEN[_ENABLED]`. Google reports fewer kills, lower kswapd CPU, improved launch times with MGLRU on.
→ CONFIDENCE: High (kernel.org docs Authority A; AOSP ACK branches Authority A; corroborating Android press Authority A).
→ IMPACT: CS-5 region tables must be **per reclaim engine** (classic LRU vs MGLRU) — strengthens the "no magic values" operating-region design (02 §6) rather than weakening it; E-06 must record MGLRU state per run and calibrate regions separately; capability probe (U-003) gains MGLRU detection. No architecture change earned: CONTINUUM's state-aware POLICY already assumes per-device calibration. Recorded as D-016.

### Q-R2 — Have lmkd `ro.lmk.*` semantics/defaults drifted on current AOSP (Android 15/16/17-era)? (Android-version-sensitive; S-002/S-003 refresh)
→ SOURCE: S-023 (AOSP lmkd master + android15-qpr1 release branch READMEs; source.android.com lmkd doc 2026-06-17)
→ LEARNED: Core property set stable (use_psi default true, psi_partial_stall_ms 70 high-end/200 low-RAM, psi_complete_stall_ms 700, kill_heaviest_task, direct_reclaim_threshold_ms, swap_compression_ratio, lowmem_min_oom_score default 701, min 201). Drift on master branch: `ro.lmk.kill_timeout_ms` default 0→100 ms; `ro.lmk.swap_free_low_percentage` default collapsed to 10 (was 10 low-RAM/20 high-end). `ro.*` = read-once-at-boot properties — runtime mutability still requires lmkd restart (E-11 assumption holds).
→ CONFIDENCE: High (Authority S/A primary).
→ IMPACT: None architectural; reaffirms S-002 LIMITATION note that OEM/version defaults vary → E-11 canary must read effective defaults per device before proposing deltas. No doc change beyond this index entry.

### Q-R3 — Is D-014's target bound (Android-based Huawei; HarmonyOS NEXT out of scope) still true in 2026? (Huawei/vendor-sensitive)
→ SOURCE: S-024 (Huawei Central 2025-07: global Pura 80 ships EMUI 15, not HarmonyOS; AndroidHeadlines 2026-08: HarmonyOS global pilot markets planned, likely HarmonyOS 6 transition w/ app compatibility)
→ LEARNED: Global Huawei flagships (Pura 80/Mate X6, 2025) still ship **EMUI 15 on an AOSP-derived base** (APK-compatible); HarmonyOS NEXT (no AOSP core) remains China-only; global expansion reportedly via pilot markets with a compatibility-retaining transition version. EMUI 15 absorbs HarmonyOS 5 features while staying Android-based.
→ CONFIDENCE: Medium-high (Authority B trade press, mutually corroborating; Huawei has no official global NEXT roadmap published).
→ IMPACT: D-014 CONFIRMED for the design horizon; REOPEN_IF extended with a market trigger (global HarmonyOS NEXT transition shipping on Huawei flagships). D-013 coexistence gains relevance: EMUI 15 remains the vendor-policy environment for global targets.

### Q-R4 — Any new *official* observation surface for cached-app freeze state? (could strengthen FX-OBS/E-05)
→ SOURCE: S-025 (source.android.com cached-apps-freezer doc, refreshed 2026-09-29)
→ LEARNED: Android 16 (API 36) adds official public APIs `IBinder.FrozenStateChangeCallback` / `addFrozenStateChangeCallback` (throws UnsupportedOperationException on kernels without binder freeze-notification support); `RemoteCallbackList` frozen-callee policies for system services. Also documented: on Android 14+ supported devices `MAX_CACHED_PROCESSES` is "significantly increased", with up to ~30% cold-start reduction attributed to keeping more apps cached — platform-side validation of the CS-3 lever rationale. Android 16 also changes `scheduleAtFixedRate` catch-up to at most one missed execution (STPE_SKIP_MULTIPLE_MISSED_PERIODIC_TASKS) — guidance for FixHUA's own schedulers (prefer fixed-delay/WorkManager patterns).
→ CONFIDENCE: High (Authority S primary platform doc).
→ IMPACT: FX-OBS gains an unprivileged freeze-state signal on API 36+ (capability-gated); E-05 measurement plan prefers the official callback over inferring from binder errors; CS-3 rationale strengthened. Refinements applied to FX-OBS contract and EXPERIMENT_REGISTRY; no architecture change.

### Q-R5 — Kernel/uclamp landscape drift on current Android GKI trains? (kernel-version-sensitive; S-004 refresh)
→ SOURCE: S-026 (source.android.com Android-common-kernel compatibility matrix; schedutil rework commit 9c0b4bb7)
→ LEARNED: GKI matrix: Android 15 → android15-6.6; Android 16 → android16-6.12; Android 17 (2026) → android17-6.18; backward compat maintained within matrix. Schedutil governor rework (merged ~6.7+, Vincent Guittot) propagates uclamp min/max as explicit performance floor/ceiling to frequency selection — uclamp hints honored more faithfully on android15-6.6/android16-6.12 than on 5.15-era kernels. PSI triggers, cgroup v2 freezer, uclamp cgroups all present on every kernel in the matrix (≥5.10).
→ CONFIDENCE: High (Authority S/A primary).
→ IMPACT: D-014's kernel ≥4.19 floor is obsolete in practice — every Android 13+ launch kernel qualifies; E-02's expected effect size may differ by kernel train (newer schedutil = cleaner boost→freq path); capability probe records kernel train. No design change; measurement interpretation note for E-02.

## S-022
TYPE: PRIMARY_PLATFORM (kernel docs) + TECHNICAL (Android enablement reporting)
TITLE: Multi-Gen LRU (kernel.org admin guide); MGLRU default-on for android14 GKI kernels (esper.io / Mishaal Rahman report)
LOCATION: https://docs.kernel.org/admin-guide/mm/multigen_lru.html ; https://www.esper.io/blog/android-dessert-bites-22-linux-memory-management-38419756 ; https://x.com/MishaalRahman/status/1714380955496976699
DATE/RECENCY: kernel doc current; Android enablement 2023-10, confirmed through android16-6.12 branches (fetched 2026-10-10)
CLAIMS_SUPPORTED: MGLRU merged Linux 6.1; backported android13-5.10/5.15; default-enabled on android14-5.15/android14-6.1 GKI and later; runtime sysfs `/sys/kernel/mm/lru_gen/enabled` (bitmask incl. 0x0007=y) + `min_ttl_ms` thrashing-prevention knob; reported effects: fewer process kills, lower kswapd CPU, improved app launch times.
LIMITATIONS: Vendor kernels may ship classic LRU or partial configs — per-device detection mandatory (feeds U-003 probe).
NOTES: Authority A (kernel.org), A (esper/Rahman, primary-adjacent reporting with AOSP references). Drives D-016.

## S-023
TYPE: SOURCE_CODE
TITLE: platform/system/memory/lmkd — master + android15-qpr1 release branch READMEs; source.android.com lmkd doc
LOCATION: https://android.googlesource.com/platform/system/memory/lmkd/+/refs/heads/master ; https://android.googlesource.com/platform/system/memory/lmkd/+/refs/heads/android15-qpr1-s5-release ; https://source.android.com/docs/core/perf/lmkd
DATE/RECENCY: source.android.com doc 2026-06-17; branches fetched 2026-10-10
CLAIMS_SUPPORTED: `ro.lmk.*` property set stable across Android 15→master; drift: `kill_timeout_ms` default 0→100 ms (master), `swap_free_low_percentage` default →10 (master); `lowmem_min_oom_score` default 701/min 201 unchanged; PSI thresholds unchanged.
LIMITATIONS: OEMs ship their own defaults; `ro.*` props are boot-time — runtime change requires lmkd restart (E-11).
NOTES: Authority S/A. Refreshes S-002/S-003; no claim overturned.

## S-024
TYPE: TECHNICAL (vendor-focused trade press, mutually corroborating)
TITLE: Global Huawei Pura 80 runs EMUI 15 not HarmonyOS (Huawei Central); Huawei ready to take HarmonyOS global via pilot markets (AndroidHeadlines)
LOCATION: https://www.huaweicentral.com/global-huawei-pura-80-series-runs-emui-15-and-not-harmonyos-psa/ ; https://www.androidheadlines.com/2026/08/huawei-take-harmonyos-global-pilot-markets.html
DATE/RECENCY: 2025-07-13 ; 2026-08-31 (fetched 2026-10-10)
CLAIMS_SUPPORTED: Global Huawei flagships (Pura 80, Mate X6) ship EMUI 15 on AOSP-derived Android with APK compatibility; HarmonyOS NEXT (no AOSP) remains China-only; global transition expected via pilot markets with a compatibility-retaining HarmonyOS 6 path.
LIMITATIONS: Trade press (Authority B); no official Huawei global NEXT roadmap — treat transition timing as UNKNOWN.
NOTES: Confirms D-014 target bounding for the design horizon; adds market-trigger REOPEN_IF.

## S-025
TYPE: PRIMARY_PLATFORM
TITLE: Cached apps freezer (source.android.com) — Android 16 frozen-state observation APIs; MAX_CACHED_PROCESSES increase
LOCATION: https://source.android.com/docs/core/perf/cached-apps-freezer
DATE/RECENCY: 2026-09-29 (fetched 2026-10-10)
CLAIMS_SUPPORTED: Android 16 (API 36) official `IBinder.FrozenStateChangeCallback`/`addFrozenStateChangeCallback` for remote-process freeze observation (UnsupportedOperationException if kernel binder lacks support); `RemoteCallbackList` frozen-callee policies; Android 14+ supported devices raise MAX_CACHED_PROCESSES significantly with up to ~30% cold-start reduction attributed; Android 16 limits `scheduleAtFixedRate` catch-up to one missed execution.
LIMITATIONS: Callback availability gated on kernel binder driver support — probe per device.
NOTES: Authority S. Refreshes S-013; strengthens CS-3 rationale; adds unprivileged OBS signal on API 36+.

## S-026
TYPE: PRIMARY_PLATFORM + SOURCE_CODE
TITLE: Android common kernels compatibility matrix (source.android.com); sched/cpufreq schedutil rework (tip commit 9c0b4bb7)
LOCATION: https://source.android.com/docs/core/architecture/kernel/android-common ; https://patchew.org/linux/170073688055.398.12687414937207369825.tip-bot2@tip-bot2/
DATE/RECENCY: matrix updated 2025-11-26 (android16-6.12 builds); commit merged 2023-11 (fetched 2026-10-10)
CLAIMS_SUPPORTED: GKI matrix — Android 15: android15-6.6; Android 16: android16-6.12; Android 17: android17-6.18; backward compat within matrix. Schedutil rework propagates uclamp min/max as explicit perf floor/ceiling to freq selection (kernels ~6.7+, i.e., android15-6.6/android16-6.12). PSI triggers/cgroup v2 freezer/uclamp cgroups present on all matrix kernels (≥5.10).
LIMITATIONS: Vendor-launch kernel may lag platform version; probe records actual train.
NOTES: Authority S/A. Measurement-interpretation note for E-02; no design change.
