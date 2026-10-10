# FINAL AUDIT PROTOCOL
## Act as a hostile independent reviewer

At this stage, stop behaving like the author of the design.

Try to prove the package is incomplete, contradictory, stale, or impossible to continue from.

---

## 1. Requirement audit

Review the original mission files and Requirement Ledger.

For every MUST verify:
- exact evidence;
- artifact/path;
- current validity;
- no stale evidence after later architecture changes.

No silent gaps.

---

## 2. Cold-restart test

Pretend all conversation context is gone.

Use only:
- final project entry file;
- project state;
- knowledge graph;
- canonical documents;
- diagrams.

Ask whether a capable new model can:
- reconstruct the product;
- identify what is real vs proposed;
- understand why the architecture exists;
- locate the next task;
- find the correct component contract;
- identify risks and unknowns;
- understand build/test flow;
- continue without inventing hidden assumptions.

Repair failures.

---

## 3. Contradiction audit

Search for contradictions among:
- project state;
- architecture documents;
- decision records;
- implementation blueprint;
- diagrams;
- tests;
- build/release description;
- risk catalog.

Do not resolve contradictions by silently deleting the inconvenient statement.

Repair or explicitly record the unresolved conflict.

---

## 4. Truth-state audit

Look for places where language accidentally upgrades:
- hypothesis → fact;
- proposal → implementation;
- experiment → production rule;
- platform possibility → device capability.

Repair them.

---

## 5. Visual/document audit

Check visual-text consistency using the Visual Twin Contract.

---

## 6. Complexity audit

Ask whether any subsystem can be removed or simplified without losing material value.

Do not ship architecture complexity merely because it is already documented.

---

## 7. Innovation audit

Ask:
- Did the design truly discover or combine mechanisms beyond superficial tweak culture?
- Are apparently numerous innovations actually duplicates?
- Which ideas are genuinely distinct?
- Which are only renamed known patterns?

Group duplicates into idea families.

Do not inflate novelty.

---

## 8. Safety/production audit

For privileged production candidates verify:
- bounded scope;
- observed capability;
- causal rationale;
- measurable gain target;
- refusal conditions;
- abort conditions;
- rollback/recovery;
- uncertainty treatment;
- no reliance on disabling fundamental hardware protections;
- no integrity/account/DRM/banking bypass.

Research-only ideas may remain more speculative but must be labeled.

---

## 9. Build-speed audit

Verify there is a credible critical path to the first useful/installable build.

Remove nonessential dependencies from the early path.

Do not remove necessary validation simply to make the plan look fast.

---

## 10. Working-memory distillation audit

Confirm that:
- important scratch discoveries were distilled;
- obsolete scratch notes did not become canonical truth;
- superseded decisions remain traceable;
- unresolved blockers were not lost.

---

## 11. Final status

Only if every mandatory requirement is SATISFIED or explicitly BLOCKED with recovery path may you proceed to Transfer Packaging.



## 12. Implementation completeness audit

Verify that the package explains the complete path from repository checkout to:
- build;
- installation;
- first-run validation;
- real-device validation;
- and daily-use readiness.

Check that component contracts, interface wiring, state ownership, startup/shutdown, failure recovery, test matrix, and first-build critical path are present.

Any unexplained integration jump is a defect.

## 13. GitHub-only continuation audit

Simulate a future model receiving only:

> “Read the project from GitHub and continue.”

Confirm that repository-root entry points and canonical documents are sufficient to:
- discover current truth;
- locate the next task;
- identify the correct source/module;
- understand the relevant contracts;
- build;
- test;
- and update project state.

If hidden conversation context is still required, repair the repository knowledge package before completion.


Otherwise:
`MISSION_INCOMPLETE`
