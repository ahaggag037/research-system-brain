# AGENTS.md — Continuation surface for `research-system-brain`

> If you are a future model told: *"Open ahaggag037/research-system-brain on GitHub, read the project, and continue from the current state"* — this file is your entry point. It is maintained at every state transition (decision D-004).

## What this repository is

The canonical workspace of **FixHUA Blind Genesis V10.3**: a single-session, clean-room engineering mission to **design FixHUA independently from first principles** — a system for Huawei-class Android phones targeting responsiveness, smoothness, low jank, multitasking continuity, fewer app reloads, better resume quality, sustained stability, workload predictability, and intelligent adaptive management (not a static tweak collection). Root access may be assumed **when justified**; root is a capability, not the product definition.

The mission contract is immutable and lives in `mission/model/`. The state machine is `mission/model/00_CONTROL_PLANE.md`. Read it if anything here conflicts.

## Hard rules (never violate)

1. **Blindness**: never inspect the user's prior `FixHUA` repositories, old prompts, old research, or parallel-model output — including anything named similarly. Public independent research is allowed, never as a backdoor. Contamination protocol: `mission/model/00B_CLEAN_REPOSITORY_BOUNDARY.md` §7.
2. **`_human/` content from the original package is excluded from this repo by decision D-002. Do not ask for it, fetch it, or reconstruct it.**
3. **Truth-state discipline** (`01` §9): every consequential claim is VERIFIED_FACT / IMPLEMENTED / TESTED / PROPOSED / EXPERIMENTAL / HYPOTHESIS / REJECTED / DEFERRED / UNKNOWN. Never promote hypothesis to fact silently.
4. **Safety** (`01` §3): no disabling hardware safety protections; no account/banking/DRM/integrity/anti-tamper bypasses; no unrestricted arbitrary-root command endpoints; privileged behavior needs evidence, bounded scope, abort conditions, and rollback.
5. **No secrets in this public repo** — no tokens, keys, personal or device-identifying data.
6. **Working memory contains operational notes, not private chain-of-thought** (`01` §11, `03` §8).

## Recovery procedure (do this first, in order)

1. `.v10/working_memory/CHECKPOINT.md` — where the mission is and what is next.
2. `.v10/working_memory/REQUIREMENT_LEDGER.md` — every obligation and its status. All MUSTs must reach SATISFIED or BLOCKED(with reason + recovery path) before any `MISSION_COMPLETE` claim.
3. `.v10/working_memory/DECISION_LOG.md` — active vs superseded decisions; never resurrect a SUPERSEDED decision without recording why.
4. `.v10/state/PROJECT_STATE.json` — machine-readable truth state.
5. The `mission/model/` file for the current mission state:
   - STATE B → `02_DISCOVERY_PROTOCOL.md`
   - STATE C → `05`, `05A`, `05B`, `06`
   - STATE D → `07`, `08`
   Read packaging/audit contracts only when their state begins (progressive disclosure, `00` §4).
6. Reconcile these files against actual repo artifacts before trusting them (`03` §5).

## Repository map

```
/
├── AGENTS.md                    ← you are here (continuation contract)
├── README.md                    ← human-facing entry
├── mission/                     ← canonical V10.3 contract snapshot (immutable)
│   └── model/                   ← 00..09 mission files (read progressively, per state)
└── .v10/
    ├── working_memory/          ← operational scratch (8 canonical files)
    └── state/                   ← PROJECT_STATE.json, KNOWLEDGE_GRAPH.json (+ DESIGN_CONVERGED when declared)
```

Planned as the mission advances (00B §3): `docs/` architecture/decision records, component contracts, implementation map, build/test/runbook, visual twin source, `audit/` final audit, `dist/` transfer package.

## Current state (updated each checkpoint — verify against files above)

- Checkpoint: **CP-001** (STATE A complete; GitHub persistence established).
- Mission state: **entering STATE B (Discovery)**.
- Architecture: **undecided by design** (R-005).
- Ledger: R-001…R-064 active; no blockers.

## Contribution protocol for continuing agents

- Keep `main` canonical (00B §6). Commit meaningful checkpoints, not noise (00B §5).
- On any consequential event, update working memory per `03` §3 write events; update this AGENTS.md at state transitions.
- Extend, never truncate, the Requirement Ledger; mark superseded items rather than deleting (`03` §6).
