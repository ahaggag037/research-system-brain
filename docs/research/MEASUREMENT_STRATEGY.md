# MEASUREMENT STRATEGY — FixHUA

> Canonical home for measurement strategy (05 §2). Ties mission outcomes (R-003, G1–G8) to concrete metrics and instruments. Success is user-visible outcomes only (01 §8).

## SLO set (PROPOSED targets; calibration on target class = U-004, gated by E-02/E-04/E-05)

| SLO | Metric | Instrument | Proposed target vs stock |
|---|---|---|---|
| SLO-1 launch/switch latency | `am start -W` TotalTime; activity display timing; p50/p90 | bench harness (E-02 protocol) | ≥10% median improvement (H-02) |
| SLO-2 jank | FrameTimeline jank rate by class (S-008); p99 frame time | Perfetto diagnostic sessions (IF-08) | measurable reduction in App/SF deadline misses during scripted sessions |
| SLO-3 continuity | reload ratio in N-app cycle; resume latency | bench harness (E-04/E-05) | reloads ↓ ≥25% at admitted settings (H-04) |
| SLO-4 sustained stability | metric drift over 30-min soak; throttle onset/duration | soak harness + thermal collector | no drift worse than stock; earlier, gentler shaping if H-05 holds |
| SLO-5 overhead | FixHUA CPU%, wakelocks, battery attribution | self-profiling + BatteryStats (E-10) | <1% CPU; no measurable battery delta (INV-8/INV-9) |
| SLO-6 predictability | variance of p90 latency across workload phases | scripted phase transitions | variance not worse than stock; ideally tightened |

## Instruments and their truth limits

- **PSI files/triggers** — kernel-ground-truth pressure (S-007); our early-warning layer (E-01).
- **FrameTimeline via Perfetto** — ground truth for jank classification (S-008); diagnostic sessions only (INV-8).
- **`am start -W` / display timing** — launch latency standard practice; noisy → ≥30 reps, medians.
- **dumpsys meminfo / lmk stats / kill logs** — kill attribution; vendor kills may not log → label detection limits (U-005).
- **BatteryStats / power HAL counters** — energy accounting; per-action attribution is approximate → treated as budget signal, not exact joules.
- **Self-profiling** — FixHUA measures its own overhead continuously (E-10 pattern) and journals budget breaches.

## Anti-gaming rules (R-045)

1. No claim from synthetic benchmarks alone; every claim needs a user-visible metric path.
2. "Free RAM", clock speeds, tweak counts are never reported as improvements.
3. A/B on same device; order randomized; medians + dispersion reported, not cherry-picked bests.
4. Regression guards co-measured (pressure stalls, battery, kills) — benefit with guard breach = failure.
5. Device-unvalidated numbers are displayed as HYPOTHESIS in UI and docs (D-015).

## Data handling

All measurement data stays on-device; exports are user-initiated bundles of device metrics only (no personal data, no app content) — SAFETY_INVARIANTS §5.
