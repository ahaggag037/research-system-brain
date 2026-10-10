# AGENTS.md — Continuation surface for `research-system-brain`

> If you are a future model told: *"Open ahaggag037/research-system-brain on GitHub, read the project, and continue from the current state"* — this file is your entry point. It is maintained at every state transition (decision D-004).

## What this repository is

The canonical workspace of **FixHUA Blind Genesis V10.3**: a single-session, clean-room engineering mission to **design FixHUA independently from first principles** — a system for Huawei-class Android phones targeting responsiveness, smoothness, low jank, multitasking continuity, fewer app reloads, better resume quality, sustained stability, workload predictability, and intelligent adaptive management (not a static tweak collection). Root access may be assumed **when justified**; root is a capability, not the product definition.

The mission contract is immutable and lives in `mission/model/`. The state machine is `mission/model/00_CONTROL_PLANE.md`. Read it if anything here conflicts.

## Hard rules (never violate)

1. **Blindness**: never inspect the user's prior `FixHUA` repositories, old prompts, old research, or parallel-model output — including anything named similarly. Public independent research is allowed, never as a backdoor. Contamination protocol: `mission/model/00B_CLEAN_REPOSITORY_BOUNDARY.md` §7.
2. **`_human/` content from the original package is excluded from this repo by decision D-002. Do not ask for it, fetch it, or reconstruct it.**
3. **Truth-state discipline** (`01` §9): every consequential claim is VERIFIED_FACT / IMPLEMENTED / TESTED / PROPOSED / EXPERIMENTAL / HYPOTHESIS / REJECTED / DEFERRED / UNKNOWN. Never promote hypothesis to fact silently. Canonical truth home: `docs/project/TRUTH_REGISTER.md`.
4. **Safety** (`01` §3): `docs/architecture/SAFETY_INVARIANTS.md` — no thermal-protection writes; no account/banking/DRM/integrity/anti-tamper bypasses; no unrestricted arbitrary-root endpoints; privileged behavior needs evidence, bounded scope, abort conditions, rollback.
5. **No secrets in this public repo** — no tokens, keys, personal or device-identifying data.
6. **Working memory contains operational notes, not private chain-of-thought** (`01` §11, `03` §8).

## Recovery procedure (do this first, in order)

1. `.v10/working_memory/CHECKPOINT.md` — where the mission is and what is next (includes 05B §8 handoff block).
2. `.v10/working_memory/REQUIREMENT_LEDGER.md` — every obligation R-001..R-114+ and its status. All MUSTs must reach SATISFIED or BLOCKED(with reason + recovery path) before any `MISSION_COMPLETE` claim (completion lock, 04 §7).
3. `.v10/working_memory/DECISION_LOG.md` — active vs superseded decisions; never resurrect a SUPERSEDED decision without recording why.
4. `.v10/state/PROJECT_STATE.json` — machine-readable truth state; `KNOWLEDGE_GRAPH.json`; `IMPLEMENTATION_MAP.json`.
5. `docs/project/00_START_HERE.md` — the knowledge-package entry (what FixHUA is, what is/isn't implemented, what is verified, what is unknown, next milestone).
6. The `mission/model/` file for the current mission state:
   - STATE C → `05`, `05A`, `05B`, `06`
   - STATE D → `07`, `08`
   Read packaging/audit contracts only when their state begins (progressive disclosure, `00` §4).
7. Reconcile these files against actual repo artifacts before trusting them (`03` §5). If they disagree: truth hierarchy in `docs/project/TRUTH_REGISTER.md` §2 — record in CONTRADICTION_REGISTER, repair, do not guess.

## Repository map

```
/
├── AGENTS.md                    ← you are here (continuation contract)
├── README.md                    ← human-facing entry
├── mission/                     ← canonical V10.3 contract snapshot (immutable)
│   └── model/                   ← 00..09 mission files (read progressively, per state)
├── docs/                        ← canonical knowledge package (STATE C — complete)
│   ├── project/                 ← 00_START_HERE, PROBLEM_MODEL, TRUTH_REGISTER
│   ├── architecture/            ← ARCHITECTURE, SAFETY_INVARIANTS, ALTERNATIVES
│   ├── components/              ← FX-OBS/MODEL/POLICY/ACT/SAFE/UI/STORE contracts
│   ├── integration/             ← INTERFACES, DATA_STATE_MODEL, RUNTIME_TRACES
│   ├── lifecycle/               ← STARTUP_SHUTDOWN
│   ├── ops/                     ← BUILD_RUNBOOK, FAILURE_RECOVERY_MATRIX, TEST_MATRIX, READY_STATES
│   ├── plan/                    ← IMPLEMENTATION_BLUEPRINT, FIRST_BUILD_CRITICAL_PATH, FAST_BUILD_PLAN
│   ├── research/                ← EXPERIMENT_STRATEGY, MEASUREMENT_STRATEGY, RISK_TRANSMUTATION_CATALOG, REJECTED_IDEAS
│   └── visuals/                 ← 10 Mermaid sources + VISUAL_TRACEABILITY.json
├── audit/                       ← final audit reports (STATE D)
├── dist/                        ← transfer package (STATE D)
└── .v10/
    ├── working_memory/          ← operational scratch (8 canonical files + DESIGN_SPACE.md)
    └── state/                   ← PROJECT_STATE.json, KNOWLEDGE_GRAPH.json, IMPLEMENTATION_MAP.json, DESIGN_CONVERGED.md
```

## Current state (updated each checkpoint — verify against files above)

- Checkpoint: **CP-003** (STATE C complete — knowledge package published).
- Mission state: **STATE D (Final Audit)** — contracts `mission/model/07_FINAL_AUDIT_PROTOCOL.md`, `08_TRANSFER_PACKAGE_SPEC.md` are now active.
- Architecture: **ARCH-FX-CP-01 "CONTINUUM"** — adaptive continuity control plane (D-006/D-007). Components: FX-OBS, FX-MODEL, FX-POLICY, FX-ACT, FX-SAFE, FX-UI, FX-STORE.
- Implementation: **FULLY_SPECIFIED (docs); zero code** — no component is IMPLEMENTED; device validation BLOCKED_BY_DEVICE_EVIDENCE (D-015; experiments E-01..E-12 ready).
- Ledger: R-001…R-114; no active blockers.
- Next: 13-audit final audit → repair loop → GitHub cold-start validation → final status proof → transfer ZIP.

## Operating rules for continuing agents (05B §2 checklist)

1. **First document:** this file, then CHECKPOINT.md, then `docs/project/00_START_HERE.md`.
2. **Current phase:** see "Current state" above (verify against PROJECT_STATE.json).
3. **Machine-readable state:** `.v10/state/PROJECT_STATE.json` (+ KNOWLEDGE_GRAPH.json, IMPLEMENTATION_MAP.json, docs/visuals/VISUAL_TRACEABILITY.json).
4. **Architecture decisions:** `.v10/working_memory/DECISION_LOG.md` (D-001..D-015; ADR-style records).
5. **Component contracts:** `docs/components/FX-*.md`.
6. **Build runbook:** `docs/ops/BUILD_RUNBOOK.md`.
7. **Test/validation matrix:** `docs/ops/TEST_MATRIX.md`.
8. **Diagrams/visual twin:** `docs/visuals/*.mmd` + traceability JSON.
9. **Truth-state rules:** Hard rule 3 + `docs/project/TRUTH_REGISTER.md`.
10. **Documentation-update rule (same-change, 05B §7):** a code change altering architecture, interfaces, behavior, privilege, safety, state schema, build, or phase updates the affected canonical docs and machine state **in the same change**. Stale docs are defects.
11. **Privileged-change admission:** only via the pipeline in `docs/architecture/SAFETY_INVARIANTS.md` §3 (UNKNOWN→OBSERVED→USEFUL→SAFE→PRODUCTION; EXPERIMENTAL gate for high-risk).
12. **Contradictions:** do not guess — apply TRUTH_REGISTER §2 hierarchy, record in CONTRADICTION_REGISTER.md, repair in the same change.
13. **Never guess missing device facts:** mark UNKNOWN + define the smallest discovery step (05 §7). No device exists in the design environment — device claims stay HYPOTHESIS (D-015).

## Contribution protocol for continuing agents

- Keep `main` canonical (00B §6). Commit meaningful checkpoints, not noise (00B §5).
- On any consequential event, update working memory per `03` §3 write events; update this AGENTS.md at state transitions.
- Extend, never truncate, the Requirement Ledger; mark superseded items rather than deleting (`03` §6).
