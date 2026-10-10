# EXPERIMENT STRATEGY — FixHUA

> Canonical home for experiment strategy (05 §2). Experiment definitions themselves live in `.v10/working_memory/EXPERIMENT_REGISTRY.md` (E-01..E-12) — that registry is the canonical record; this document is the strategy that produced and sequences it.

## Principles (02 §7)

1. **Information gain per test**, not test count: each experiment falsifies the most consequential uncertainty available.
2. **Multi-branch falsification preferred:** E-01 (PSI fidelity) gates OBS design AND policy reaction times AND E-11 feasibility.
3. **Risk-proportional evidence:** observation-only ideas need little; privileged production candidates need OBSERVED→USEFUL→SAFE chains (admission pipeline).
4. **No device → no device claims** (D-015): all E-xx are READY/PROPOSED harnesses; none has run.

## Dependency order

```
E-12 (capability detection) ──┐
E-01 (PSI fidelity) ──────────┼──► E-02 (uclamp boost) ──► E-04 (continuity) ──► FB-2 promotions
E-10 (overhead) ──────────────┘         │
E-03 (adj overwrite) ───────────────────┤
E-05 (freezer resume) ──────────────────┘
E-06 (vm regions) ──► region tables v2          E-08 (thermal lead) ──► CS-8 arm/disarm
FB-1 data ──► E-09 (prediction accuracy) ──► CF-6 arm/heuristic fallback
E-11 (lmkd retune) — gated behind E-01..E-06, tier-3 canary only
```

## Cross-falsification map

| Experiment | Falsifies/confirms |
|---|---|
| E-01 | H-01; OBS trigger design; POLICY min reaction time; E-11 precondition |
| E-02 | H-02; CS-1 production admission; userspace-ceiling assumption A2 (partial) |
| E-03 | H-03; CS-2 viability vs policy-level levers |
| E-04 | H-04; CS-3 production admission; continuity thesis (FF-3) |
| E-05 | freezer cost model; keep-warm sizing; CS-4 disposition |
| E-06 | vm region table (no-magic-values requirement R-070); CS-5 admission |
| E-07 | CS-6 admission; FF-2(d) I/O amplifier model |
| E-08 | H-05; CS-8 advisory viability vs reactive demotion |
| E-09 | H-06; CF-6 arm vs heuristic fallback; D-012 calibration |
| E-10 | H-07; INV-8 budget; OBS production sampling rates |
| E-11 | CS-9 disposition (production proposal vs research-only archive) |
| E-12 | U-005; CS-10 automation depth; probe-driven admission |

## Method rules

- A/B on the same device wherever possible (stock vs FixHUA), order-randomized, ≥30 reps for latency claims.
- Regression guards measured simultaneously (PSI full stalls, battery, kill counts) — benefit at the expense of a guard = FAIL (02 §5: measure both benefit and risk).
- Results recorded in the registry with device/build labels; promotion decisions reference registry entries (R-062 evidence discipline).
- Any experiment may end INCONCLUSIVE — never force a verdict (ABORTED/INCONCLUSIVE are first-class outcomes).
