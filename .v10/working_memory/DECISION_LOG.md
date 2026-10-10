# DECISION LOG

## Decision template

DECISION_ID:
STATUS: ACTIVE | SUPERSEDED | REJECTED | PROVISIONAL
DATE/SEQUENCE:
CONTEXT:
OPTIONS:
DECISION:
EVIDENCE:
ASSUMPTIONS:
CONSEQUENCES:
RISKS:
REOPEN_IF:
SUPERSEDES:
SUPERSEDED_BY:

---

## D-001 — Repository layout
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 1 (STATE A)
CONTEXT: Empty authorized repo; 00B §3 requires eventual canonical surfaces; need a layout that keeps mission contract, working memory, machine state, and future product docs cleanly separated.
OPTIONS: (a) flat root dump; (b) `mission/` + `.v10/` + future `docs/` separation; (c) docs-only repo with memory elsewhere.
DECISION: Layout (b): `mission/` = immutable canonical V10.3 contract snapshot (excluding `_human/`); `.v10/working_memory/` = operational scratch (03 §1); `.v10/state/` = machine-readable state; `docs/`, `audit/`, `dist/` to be added in STATE C/D; root `AGENTS.md` + `README.md` as entry surfaces.
EVIDENCE: 00B §3–§4 layout guidance; 03 §1.
ASSUMPTIONS: Future states will not need layout migration; if they do, recheck per 04 §5.
CONSEQUENCES: Clean cold-start navigation; mission contract travels with repo.
RISKS: Low; dot-directory `.v10` is visible on GitHub, acceptable.
REOPEN_IF: A packaging contract (STATE C) mandates a different canonical layout.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-002 — Exclusion of `_human/` from repository snapshot
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 2 (STATE A)
CONTEXT: Package contains `_human/ARCHITECTURE_REVIEW.md` marked `canonical: false` in manifest; 00 §1 forbids the executing model from reading `_human/`; 00B §8 cold-start test means a future model will read this repo.
OPTIONS: (a) include `_human/` unread; (b) exclude it entirely.
DECISION: Exclude `_human/` from the repo. Never read it (R-010). Excluding it also protects the blind condition for any future cold-start model continuing from GitHub.
EVIDENCE: PACKAGE_MANIFEST.json (`canonical: false`); 00 §1; 00B §8.
ASSUMPTIONS: User retains `_human/` in the original ZIP outside the repo.
CONSEQUENCES: Repo holds only model-canonical mission files.
RISKS: None identified; the file is human-facing by design.
REOPEN_IF: User explicitly instructs otherwise.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-003 — GitHub API is the persistence path (not sandbox git push)
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 3 (STATE A)
CONTEXT: Sandbox has git but no GitHub credentials; authenticated GitHub API access (login `ahaggag037`) IS available this session via the GitHub integration.
OPTIONS: (a) attempt git push and fail; (b) commit via API file operations; (c) local-only persistence with blocker note.
DECISION: Commit via authenticated GitHub API directly to `main`. Local working copy at sandbox path is a staging mirror, not canonical.
EVIDENCE: Authenticated identity verified; repo branch list initially empty; API write confirmed at CP-001 push.
ASSUMPTIONS: API access persists for the session. If it fails mid-mission → blocker protocol R-024 (record, smallest recovery = retry/alternate batching; continue local work).
CONSEQUENCES: Commit granularity = meaningful checkpoints (00B §5), not per-file noise.
RISKS: API rate limits on large batches → mitigate with batched multi-file commits.
REOPEN_IF: API write fails repeatedly.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-004 — AGENTS.md as the cold-start continuation surface
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 4 (STATE A)
CONTEXT: 00B §3 requires root AGENTS.md; 00B §8 cold-start test needs an unambiguous entry that routes a future model to checkpoint → ledger → decisions → current state file (03 §5).
OPTIONS: (a) README-only; (b) AGENTS.md as machine-continuation entry + README as human entry; (c) single combined doc.
DECISION: (b). AGENTS.md = operational continuation contract; README.md = human-facing project entry.
EVIDENCE: 00B §3, §8; 03 §5.
ASSUMPTIONS: Future models check AGENTS.md by convention.
CONSEQUENCES: AGENTS.md must be updated at every state transition (add to checkpoint checklist).
RISKS: Staleness → mitigated by checkpoint discipline (R-051).
REOPEN_IF: Final audit finds cold-start ambiguity.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-005 — Full mission contract snapshot staged to GitHub before deep Discovery
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 5 (STATE A, user-directed)
CONTEXT: User directive (mission restart turn): "Before beginning deep Discovery, make the mission recoverable from GitHub: persist the canonical mission bootstrap/control files and initialized working-memory state." 00 §4 progressive disclosure exists to protect discovery from documentation pressure — a readability concern, not a secrecy one.
OPTIONS: (a) push only STATE A files, defer 02/05–09 until their states; (b) stage all 16 model files now per user directive.
DECISION: (b). All `mission/model/` files pushed at CP-001. Progressive *activation* is preserved: 02 governs STATE B (begins now); 05/05A/05B/06 govern packaging and are inert until DESIGN_CONVERGED; 07/08 inert until STATE D. R-017 (no final-doc polish while architecture moves) unaffected.
EVIDENCE: User directive text; R-064.
ASSUMPTIONS: Visibility of later contracts does not bias discovery toward premature packaging; discovery protocol 02 §1–§2 keeps focus on problem-first work.
CONSEQUENCES: GitHub alone is sufficient to recover the full mission after any reset (the user's stated goal). Ledger notes record the staging-vs-activation distinction.
RISKS: Minor anchoring risk from packaging contracts; mitigated by explicit inert-until-state rule.
REOPEN_IF: User revokes directive or control plane reinterpretation required.
SUPERSEDES: —
SUPERSEDED_BY: —
