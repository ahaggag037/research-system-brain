# COMPLIANCE SHELL
## Strict completeness around a free creative core

This file exists so creativity does not cause requirement loss.

It does not prescribe the architecture.

---

## 1. Requirement Compiler

At initialization, convert all currently loaded mandatory instructions into atomic requirements:

`R-001, R-002, ...`

Each requirement must contain:
- source file/section;
- requirement text in your own concise words;
- priority: MUST / SHOULD / MAY;
- acceptance evidence;
- current status;
- artifact/path that will prove completion;
- dependencies or blockers.

When later mission files are loaded, extend the ledger.

Do not replace the source contract with the ledger.

---

## 2. No silent prioritization

Do not give a requirement more importance merely because:
- it appears later,
- it is bolded,
- it is easier,
- it is more interesting,
- it matches your preferred domain,
- or another requirement took more space in the prompt.

Mandatory requirements remain mandatory until resolved.

---

## 3. Depth proportionality

Do not force equal word counts.

Depth should be proportional to:
- consequence,
- complexity,
- uncertainty,
- implementation sensitivity,
- and risk.

A sensitive architectural mechanism cannot be “covered” by a vague sentence while a decorative topic receives pages.

---

## 4. Requirement-to-artifact binding

Every MUST should eventually point to concrete evidence:
- document section,
- diagram,
- schema,
- test plan,
- decision record,
- implementation blueprint,
- or explicit blocker.

“Discussed” is not the same as “satisfied.”

---

## 5. Architecture-change recheck

When a major design change occurs:
- identify affected requirements;
- reopen any requirement whose prior evidence is no longer valid;
- update affected decisions and notes.

Do not carry stale compliance forward.

---

## 6. Conflict Register

If two requirements appear to conflict:
- assign a conflict ID;
- state the exact conflict;
- state why both cannot be fully satisfied simultaneously;
- state the resolution;
- state what was sacrificed;
- state whether user clarification would change the result.

Do not use “conflict” as a convenient excuse to drop a difficult requirement.

---

## 7. Completion lock

The quality of one part of the mission does not compensate for silent omission elsewhere.

Before `MISSION_COMPLETE`:
- every MUST must be SATISFIED or BLOCKED;
- every BLOCKED item must include a precise reason and recovery path;
- no MUST may remain silently OPEN.

A brilliant architecture with a missing mandatory handoff is not complete.

---

## 8. Evidence statuses

Recommended statuses:
- OPEN
- IN_PROGRESS
- SATISFIED
- PARTIALLY_SATISFIED
- BLOCKED
- SUPERSEDED
- NOT_APPLICABLE_WITH_JUSTIFICATION

Do not mark SATISFIED without pointing to evidence.

---

## 9. Compliance checkpoints

Perform a compact ledger review:
- after initialization;
- after major architecture changes;
- at DESIGN_CONVERGED;
- after Knowledge Packaging;
- before final ZIP creation.

Do not continuously interrupt creative work for minor ledger bookkeeping.

The shell protects the mission; it should not dominate it.
