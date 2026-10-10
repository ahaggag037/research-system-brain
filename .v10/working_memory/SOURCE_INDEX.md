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
