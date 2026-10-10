# ALTERNATIVES — major architectures considered and why they lost

> Canonical home for architecture alternatives (05 §2). Full adversarial detail: `.v10/working_memory/DESIGN_SPACE.md` §4–§5. Selection decision: D-007.

## ARCH-A — Static tuning pack ("apply good values once")

- **Pitch:** ship curated sysctl/props/device_config values.
- **Why it lost:** the mission's explicit anti-goal (00 §0: intelligent management, not static tweaks). Effects are state-dependent (02 §6) — one value cannot be right across idle/launch/soak. No feedback → no predictability under changing workloads (G7).
- **Salvaged:** region *tables* (state-dependent parameter sets) live inside FX-POLICY; documented levers (CS-3/CS-4) are applied statelessly where evidence says they are unconditionally safe.
- **Reopen if:** E-series shows state-dependence negligible on target class (would collapse FixHUA to a tuner — evidence contradicts mission goals; recorded for honesty).

## ARCH-B — Userspace adaptive control plane → **became ARCH-FX-CP-01**

Selected. See `ARCHITECTURE.md`.

## ARCH-C — Kernel module / kernel patch (deep control: scheduler hooks, custom reclaim policy)

- **Pitch:** maximum authority; can implement TPP-class prefetch natively (S-016).
- **Why it lost (v1):** breaks hours-to-first-build (02 §8), device-specific kernel builds, rollback/integrity hazards (boot loop class), conflicts with INV-6-class reversibility on stock OTAs. Userspace ceiling not yet proven (A2 in PROBLEM_MODEL §6).
- **Status:** DEFERRED, research-only. Reopen condition (D-007): E-series evidence shows userspace ceiling blocks SLO attainment — then a bounded, device-specific module may be designed with the admission pipeline from day one.

## ARCH-D — App-only (no root)

- **Pitch:** zero privilege risk; store-installable.
- **Why it lost (as the whole product):** cannot touch the control surfaces where the mechanisms live (CS-1/2/5/6/7/9 all privileged); value capped at diagnosis + guidance.
- **Salvaged:** it **is** the degraded mode (D-009) and the FB-1 observer baseline (D-008). Kept as a first-class supported configuration, not a fallback embarrassment.

## ARCH-E — ROM mod / firmware integration

- **Pitch:** deepest clean integration (like vendor perfmgr).
- **Why it lost:** not installable on stock devices; excludes the entire target user base; multiplies EMUI variance risk (RSK-109) by firmware coupling.

## Category-level rejects (pre-architecture)

- **Booster/cleaner app** — actively harmful to continuity goals (G3/G4); see `../research/REJECTED_IDEAS.md` RI-01.
- **Diagnostic-only tool** — valuable but cannot meet G1–G7 alone; absorbed as FB-1 stage instead.
- **Static AI-less policy daemon without admission control** — fails 01 §3/§6; absorbed with SAFE added.

## Challenger discipline record (02 §9)

Challengers were maintained through STATE B until ARCH-FX-CP-01 dominated on: evidence base (mechanism sources), safety admission fit, hours-to-first-build, and reversibility. Adversarial attack (10 vectors, DESIGN_SPACE §5) produced design changes (telemetry budget, single-writer rule, low-regret-only prediction arms, EXPERIMENTAL gate for lmkd retune) rather than collapse.
