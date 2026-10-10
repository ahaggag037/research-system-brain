# ACTIVE NOTES

Keep this concise.

## Current high-value facts
- F-001: Authorized repo `ahaggag037/research-system-brain` (ID 1412675689), `main`, public; CP-001 pushed and verified byte-exact (30 files).
- F-002: GitHub write access via authenticated API (D-003); sandbox git read-only for GitHub.
- F-006: lmkd tunable via `ro.lmk.*` (PSI partial 70 ms / complete 700 ms defaults; kill_heaviest_task; lowmem_min_oom_score 701) — VERIFIED_FACT platform-level (S-002/S-003); runtime application needs lmkd restart → EXPERIMENTAL (E-11).
- F-007: uclamp per-task + cgroup v2 `cpu.uclamp.min/max` control freq selection and big.LITTLE placement — VERIFIED_FACT (S-004).
- F-008: PSI triggers are poll-able, window 500 ms–10 s, rate-limited per window; per-cgroup pressure exists — VERIFIED_FACT (S-007).
- F-009: Cached-app freezer (Android 11+): freeze ~10 s after cached (14+), compaction on freeze, `use_freezer` + `max_cached_processes` device_config overrides documented — VERIFIED_FACT (S-013).
- F-010: ART CC GC pause small (~2 ms class) — VERIFIED_FACT (S-006); jank taxonomy measurable via FrameTimeline (S-008).
- F-011: EMUI layered background kills (App-launch manager, cleanup, PowerGenie-class) — field evidence (S-010); HarmonyOS NEXT is NOT Android → target bounded to Android-based EMUI/HarmonyOS ≤4 (S-020, D-014).
- F-012: ADPF PerformanceHint sessions prove platform-sanctioned adaptive hints (S-011); APPM proves next-app prediction without training/sensors (S-015).
- F-013: extra_free_kbytes exists to keep bursts out of direct reclaim (S-017); swappiness >100 rational with fast zram (S-018) — state-aware VM regions justified.

## Current hypotheses (device-gated)
- H-01: PSI triggers give ≥100 ms early warning before allocstall spikes (E-01).
- H-02: Bounded uclamp boost at launch cuts launch latency ≥10% (E-02).
- H-03: oom_score_adj nudges survive between AMS recomputes usefully (E-03).
- H-04: max_cached_processes +8 cuts reloads ≥25% without PSI regression (E-04).
- H-05: Thermal zones give ≥10 s throttle lead time (E-08).
- H-06: Next-app Accuracy@3 ≥60% makes low-regret arming positive-EV (E-09).
- H-07: Production OBS overhead <1% CPU (E-10).

## High-potential ideas (dispositioned — see DESIGN_SPACE §3)
- CF-1 observation plane (production foundation), CF-2 transition uclamp, CF-3 memory continuity governance, CF-4 I-O coordination (+experimental prefetch), CF-5 thermal advisory shaping, CF-6 predictive arming (low-regret only), CF-7 vendor coexistence. Rejected: CF-8 kernel mods v1, CF-9 RAM cleaning, CF-10 clock pinning, CF-11 global freezer disable.

## Immediate next actions
1. Declare DESIGN_CONVERGED (marker + commit CP-002). [done]
2. Enter STATE C: ledger extension (packaging requirements from 05/05A/05B/06), then distill DESIGN_SPACE → docs/.
3. Push CP-003 when knowledge package substantially complete.

## Things that must not be forgotten
- Blindness rules R-008/R-029/R-030 remain active; zero contamination events so far.
- R-017 lifts at DESIGN_CONVERGED; docs/ may now be created (they are the deliverable).
- Truth discipline: no device in environment → device claims stay HYPOTHESIS; readiness labels per 05A §13 (D-015).
- Ledger extension owed at STATE C entry (R-055); AGENTS.md update at transition (D-004).
- Repo public: no secrets/device identifiers (R-032).
