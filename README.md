# research-system-brain

Canonical workspace for **FixHUA Blind Genesis V10.3** — an independent, clean-room engineering mission to design **FixHUA**: an intelligent system for Huawei-class Android phones aimed at responsiveness, smoothness, low jank, multitasking continuity, fewer avoidable app reloads/exits, better resume quality, sustained stability, and predictability under changing workloads.

This repository is the primary continuation surface for the mission. Conversation history is not canonical.

## Status

| | |
|---|---|
| Mission state | STATE D (Final Audit) executing — 13-audit pass done, repairs applied; packaging next |
| Architecture | **ARCH-FX-CP-01 "CONTINUUM"** — adaptive continuity control plane (D-006/D-007) |
| Implementation | FULLY_SPECIFIED (documentation); no code yet; device validation BLOCKED_BY_DEVICE_EVIDENCE |
| Checkpoint | CP-005 |
| Requirements | R-001…R-133 — all MUSTs SATISFIED or closing at handoff (`.v10/working_memory/REQUIREMENT_LEDGER.md`) |
| Knowledge package | [`docs/project/00_START_HERE.md`](docs/project/00_START_HERE.md) |

## Where to start

- **Continuing agents**: read [`AGENTS.md`](AGENTS.md) first — it is the maintained continuation surface.
- **Project entry (humans + agents)**: [`docs/project/00_START_HERE.md`](docs/project/00_START_HERE.md).
- **Mission contract**: [`mission/model/00_CONTROL_PLANE.md`](mission/model/00_CONTROL_PLANE.md) (progressive disclosure: later files unlock per state).
- **Live mission state**: [`.v10/state/PROJECT_STATE.json`](.v10/state/PROJECT_STATE.json) and [`.v10/working_memory/`](.v10/working_memory/).

## Constraints

Clean-room isolation (no predecessor FixHUA material), truth-state discipline, root-as-capability-not-definition, and production-admission safety rules apply to everything in this repo. See `AGENTS.md` §Hard rules and [`docs/architecture/SAFETY_INVARIANTS.md`](docs/architecture/SAFETY_INVARIANTS.md).
