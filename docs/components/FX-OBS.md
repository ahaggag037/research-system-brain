# Component contract — FX-OBS

- **Stable ID:** FX-OBS
- **Proposed source path:** `app/obs/` (Kotlin collectors) + `app/obs/native/` (JNI for PSI triggers) — PROPOSED, not implemented
- **Runtime owner:** app process (foreground service, no wakelock-held polling)
- **Lifecycle:** started at app start / BOOT_COMPLETED (deferred); stopped on user disable or kill switch
- **Diagrams:** `../visuals/COMPONENT_RELATIONS.mmd`, `NORMAL_RUNTIME.mmd`
- **Decision references:** D-008 (observation-first), D-013 (vendor detection), D-015

## Contract

- **Purpose:** produce all sensing for the control plane: early pressure signals, lifecycle events, contention counters, vendor-policy state, thermal trends; run diagnostic traces on demand. Sole writer of telemetry events.
- **Inputs:** kernel PSI files (`/proc/pressure/*`, cgroup pressure files where readable); `/proc/vmstat`, `/proc/stat`, thermal zone readings; framework callbacks (ActivityManager process states, UsageStats events with user grant); device_config change notifications; Perfetto sessions (diagnostic mode only).
- **Outputs:** typed telemetry event stream (`ObsEvent` schema, `schemas/obs_event.schema.json` — PROPOSED) to FX-MODEL/FX-STORE; capability probe results to FX-STORE capability registry; diagnostic trace bundles to FX-UI.
- **Preconditions:** app installed; for full signal set: UsageStats grant; for cgroup-level PSI: readable pressure files (probe E-01/E-12); root NOT required for baseline set.
- **Postconditions:** events delivered at ≤1 Hz steady rate with burst-on-trigger; probe matrix persisted; overhead within budget (INV-8).
- **Invariants:** never writes any control surface; never holds a wakelock for steady-state sampling; PSI trigger windows within kernel-allowed 500 ms–10 s (S-007); Perfetto never auto-started in production mode.
- **Side effects:** negligible by design (E-10 gate); diagnostic Perfetto sessions are user-initiated and time-boxed.
- **Dependencies:** FX-STORE (registry persistence); Android framework APIs; kernel PSI/cgroup availability (capability-gated). Freeze-state signal: Android 16+ `IBinder.FrozenStateChangeCallback` where kernel binder supports it (S-025), else trace-inferred. Reclaim-engine detection (classic LRU vs MGLRU) via `/sys/kernel/mm/lru_gen/*` per D-016.
- **Failure behavior:** a failing collector degrades that signal class to UNKNOWN (MODEL treats as missing input, never guesses); collector crash isolated per collector.
- **Recovery behavior:** supervisor restarts failed collector with backoff; persistent failure → signal marked BLOCKED in capability registry; UI shows degraded badge.
- **Concurrency assumptions:** collectors on dedicated serial executors; event bus is single-threaded ordered; no shared mutable state outside STORE.
- **Privilege assumptions:** none required for baseline; optional root probe hooks via FX-ACT read-commands only.
- **Performance budget:** <1% average CPU total telemetry (E-10); event burst < 50 events/s sustained.
- **Configuration surface:** sampling profile (steady/diagnostic), per-signal enables, probe schedule — all in STORE config with safe defaults.
- **Test obligations:** unit: event schema, trigger parsing; integration: simulated pressure via test allocator; device: E-01, E-10, E-12; overhead regression test in CI emulator where signals exist.
- **Must not do:** actuate; sample faster than configured; infer unobservable device facts (mark UNKNOWN instead); run Perfetto always-on; collect personal data or app content.
