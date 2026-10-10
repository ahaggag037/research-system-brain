# TRUTH REGISTER — source-of-truth ownership, conflict resolution, current truth states

> Canonical home for: verified platform facts, assumptions, unknowns, truth hierarchy, conflict-resolution procedure (05 §3; 05B §6). Working-memory files link here; they do not redefine truth.

## 1. Source-of-truth ownership (one canonical home per consequential fact)

| Fact/decision class | Canonical home | Everywhere else |
|---|---|---|
| Mission obligations | `.v10/working_memory/REQUIREMENT_LEDGER.md` | linked |
| Architecture decisions | `.v10/working_memory/DECISION_LOG.md` (D-xxx) | linked as "ADR D-xxx" |
| Risks | `.v10/working_memory/RISK_REGISTER.md` (RSK-xxx) | linked |
| Experiments | `.v10/working_memory/EXPERIMENT_REGISTRY.md` (E-xx) | linked |
| External evidence | `.v10/working_memory/SOURCE_INDEX.md` (S-xxx) | linked |
| Problem model / goals / non-goals | `docs/project/PROBLEM_MODEL.md` | linked |
| Truth states, ownership, hierarchy | this file | linked |
| Architecture & control logic | `docs/architecture/ARCHITECTURE.md` | linked |
| Safety invariants | `docs/architecture/SAFETY_INVARIANTS.md` | linked |
| Component behavior | `docs/components/FX-*.md` | linked |
| Interfaces & state schemas | `docs/integration/` | linked |
| Build & ops truth | `docs/ops/` | linked |
| Implementation order | `docs/plan/` | linked |
| Machine state | `.v10/state/PROJECT_STATE.json`, `KNOWLEDGE_GRAPH.json`, `IMPLEMENTATION_MAP.json` | linked |
| Visual truth | `docs/visuals/*.mmd` + `VISUAL_TRACEABILITY.json` | linked |
| Checkpoint/handoff | `.v10/working_memory/CHECKPOINT.md` | linked |

## 2. Repository truth hierarchy (05B §6)

**"What currently runs"** (descending authority):
1. verified device evidence (E-series results, labeled with device/build);
2. current checked-out code;
3. current tests/CI results;
4. current build/release artifacts;
5. current project state/documentation.

**"Why it is designed this way"** (descending authority):
1. active (non-SUPERSEDED) decision records D-xxx;
2. `docs/architecture/ARCHITECTURE.md`;
3. component contracts `docs/components/`;
4. implementation blueprint `docs/plan/IMPLEMENTATION_BLUEPRINT.md`.

**Conflict procedure:** do not guess. Record in `.v10/working_memory/CONTRADICTION_REGISTER.md` (ID, exact conflict, sources in conflict, resolution, sacrifice, whether clarification would change result — 04 §6), repair the artifact that lost per the hierarchy above **in the same change**, and note the repair in CHECKPOINT.md.

## 3. Verified facts (VERIFIED_FACT)

| ID | Fact | Source |
|---|---|---|
| F-001 | Repo facts: `ahaggag037/research-system-brain`, branch `main`, public; API write path confirmed | session evidence |
| F-002 | GitHub API write available (login ahaggag037); sandbox git has no push credentials | session evidence, D-003 |
| F-003 | Mission package integrity: SHA256SUMS 16/16 OK at intake | PACKAGE_MANIFEST verification |
| F-004 | Target outcomes = responsiveness, smoothness, low jank, continuity, fewer reloads, resume quality, sustained stability, predictability, intelligent management | mission 00 §0 |
| F-005 | Root assumed available when justified; root ≠ product definition | mission 00 §0 |
| F-006 | lmkd tunable via `ro.lmk.*` (PSI thresholds, kill policy knobs) | S-002/S-003 |
| F-007 | uclamp per-task + cgroup v2 `cpu.uclamp.min/max` semantics; freq/placement influence | S-004 |
| F-008 | PSI poll-able triggers `<some\|full> <stall_us> <window_us>`; per-cgroup pressure files | S-007 |
| F-009 | Cached-app freezer behavior; `use_freezer`, `max_cached_processes` device_config overrides | S-013 |
| F-010 | ART Concurrent Copying GC short pauses; FrameTimeline jank taxonomy | S-006/S-008 |
| F-011 | EMUI layered background management; HarmonyOS NEXT/5 not Android | S-010/S-020 |
| F-012 | ADPF performance-hint sessions exist platform-side; next-app prediction feasible (APPM) | S-011/S-015 |
| F-013 | `vm.extra_free_kbytes` purpose; swappiness 0–200 semantics with zram | S-017/S-018 |
| F-014 | Perfetto platform tracing with programmable config; too heavy for always-on telemetry | S-021 |
| F-015 | App standby buckets incl. restricted bucket; phantom process cap (32) with toggle | S-012/S-014 |

## 4. Hypotheses (device-dependent; all gated by experiments)

| ID | Hypothesis | Gate |
|---|---|---|
| H-01 | PSI triggers fire early enough to preempt user-visible stalls | E-01 |
| H-02 | Bounded uclamp boost cuts launch latency ≥10% median on target class | E-02 |
| H-03 | External oom_score_adj nudges persist usefully between AMS recomputes | E-03 |
| H-04 | Raising max_cached_processes within memory budget cuts reloads ≥25% without pressure regression | E-04 |
| H-05 | Thermal lead time ≥10 s before throttle on target class | E-08 |
| H-06 | Next-app prediction Accuracy@3 ≥60% with abstention | E-09 |
| H-07 | Production telemetry overhead <1% CPU | E-10 |

## 5. Critical unknowns (with smallest discovery step)

| ID | Unknown | Smallest step |
|---|---|---|
| U-003 | Per-device control writability/ownership (cgroup, device_config scope) | E-01/E-03/E-12 probes on device |
| U-004 | Quantitative SLO budgets attainable on target class | E-02/E-04/E-05 measurements |
| U-005 | EMUI version surface variance | E-12 capability matrix |
| U-006 | Thermal headroom predictability | E-08 |
| U-007 | Predictor accuracy on real usage | E-09 (needs FB-1 data) |

## 6. Current package completeness statement (05A §13)

- Architecture: **FULLY_SPECIFIED** (ARCH-FX-CP-01; survived self-attack; challengers recorded).
- Implementation: **FULLY_SPECIFIED** at documentation level; code = **not started** (zero implemented components).
- Device validation: **BLOCKED_BY_DEVICE_EVIDENCE** — no physical device in the design environment (D-015); E-01..E-12 ready-to-run.
- Tooling: not blocked for FB-1 (Android SDK/NDK standard; exact versions UNKNOWN → smallest verification step in `../ops/BUILD_RUNBOOK.md` §1).

Nothing in this package is claimed TESTED or FULLY_TESTED_ON_TARGET.
