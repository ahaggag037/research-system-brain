# research-system-brain

Canonical workspace for **FixHUA Blind Genesis V10.3** — an independent, clean-room engineering mission to design **FixHUA**: an intelligent system for Huawei-class Android phones aimed at responsiveness, smoothness, low jank, multitasking continuity, fewer avoidable app reloads/exits, better resume quality, sustained stability, and predictability under changing workloads.

This repository is the primary continuation surface for the mission. Conversation history is not canonical.

## Status

| | |
|---|---|
| Mission state | STATE A complete → STATE B (Discovery) |
| Architecture | Undecided (by design — see `.v10/working_memory/DECISION_LOG.md`) |
| Checkpoint | CP-001 |
| Requirements | R-001…R-064 (`.v10/working_memory/REQUIREMENT_LEDGER.md`) |

## Where to start

- **Continuing agents**: read [`AGENTS.md`](AGENTS.md) first — it is the maintained continuation surface.
- **Mission contract**: [`mission/model/00_CONTROL_PLANE.md`](mission/model/00_CONTROL_PLANE.md) (progressive disclosure: later files unlock per state).
- **Live mission state**: [`.v10/state/PROJECT_STATE.json`](.v10/state/PROJECT_STATE.json) and [`.v10/working_memory/`](.v10/working_memory/).

## Constraints

Clean-room isolation (no predecessor FixHUA material), truth-state discipline, root-as-capability-not-definition, and production-admission safety rules apply to everything in this repo. See `AGENTS.md` §Hard rules.
