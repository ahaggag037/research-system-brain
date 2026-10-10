# KNOWLEDGE PACKAGE CONTRACT
## Read only after DESIGN_CONVERGED

Now convert the converged design into a durable project knowledge system.

The final package is not a pile of notes and not one giant report.

Design the documentation architecture itself.

---

## 1. Canonical entry point

Create a short AI/engineer entry file such as:
- `AGENTS.md`,
- `AI_START_HERE.md`,
- or a better equivalent.

It must tell a future model:
- what to read first;
- where current truth lives;
- how to distinguish implemented from proposed work;
- where architecture decisions live;
- what not to guess;
- how to handle contradictions;
- how privileged changes are admitted;
- and what documentation must change with code.

Keep it short enough to be read.

---

## 2. Required knowledge domains

Organize them however your architecture deserves, but preserve enough information to reconstruct:

- problem definition;
- user-visible goals;
- success model;
- non-goals;
- verified device/platform facts;
- assumptions and unknowns;
- selected product thesis;
- architecture;
- major alternatives and why they lost;
- subsystem responsibilities;
- interfaces/contracts;
- data/state models;
- privilege model;
- runtime flows;
- decision/control logic;
- safety invariants;
- failure handling;
- recovery/rollback;
- measurement strategy;
- experiment strategy;
- build/release process;
- signing/secrets boundaries;
- implementation order;
- code/module map;
- current project state;
- open questions;
- deferred/high-potential ideas;
- rejected ideas;
- future-model operating instructions.

---

## 3. Source-of-truth ownership

Every consequential fact or decision should have one canonical home.

Other documents should link rather than silently duplicate.

Create a clear conflict-resolution procedure for:
- device evidence,
- source code,
- tests/CI,
- build artifacts,
- architecture decisions,
- planning documents.

A future model must know what wins when artifacts disagree.

---

## 4. Decision records

Record important architectural decisions with stable IDs.

A decision record should include:
- context;
- alternatives;
- selected choice;
- evidence;
- assumptions;
- consequences;
- risks;
- reopen conditions;
- superseded decision if any.

Never silently rewrite project history.

---

## 5. Machine-readable state

Create a machine-readable project state file.

It should expose at least:
- project phase;
- selected architecture ID;
- implemented/unimplemented subsystems;
- verified facts;
- critical unknowns;
- active risks;
- build state;
- test state;
- current blockers;
- next recommended milestone.

---

## 6. Knowledge graph

Create a machine-readable relationship graph mapping:
- requirements;
- decisions;
- mechanisms;
- components;
- data/state;
- source modules;
- tests;
- diagrams;
- risks;
- experiments;
- implementation stages.

The exact format is your choice.

Navigability matters more than format.

---

## 7. Implementation blueprint

For each implementation stage include where relevant:
- purpose;
- dependencies;
- files/modules;
- classes/interfaces;
- data models;
- state transitions;
- algorithms/pseudocode;
- concurrency expectations;
- privilege requirements;
- error handling;
- rollback;
- test obligations;
- build obligations;
- real-device validation;
- entry criteria;
- exit criteria;
- parallelizable work.

Do not fabricate device-specific controls or paths that were never observed.

Mark them UNKNOWN and define the smallest discovery step.

---

## 8. Fast-build plan

Create a dedicated shortest-path plan to:
1. first useful build;
2. first installable/testable build;
3. first daily-usable build.

Include:
- critical path;
- parallel work;
- blockers;
- minimum sufficient tests;
- what is intentionally deferred;
- expected evidence before moving forward.

The objective is speed through sequencing, not superficiality.

---

## 9. Risk-transmutation catalog

Preserve unconventional high-impact ideas.

For each important one record:
- raw idea;
- expected benefit;
- why it is considered risky;
- actual hazard mechanism;
- model/reasoning;
- redesigned bounded version;
- observations required;
- uncertainty;
- abort conditions;
- rollback;
- shortest falsification experiment;
- current disposition.

---

## 10. Rejected-idea memory

For important rejected ideas record:
- why it looked promising;
- what invalidated it;
- whether rejection is permanent or conditional;
- what new evidence would reopen it.

Prevent future models from repeating dead ends without new evidence.

---

## 11. Documentation maintenance rule

Any future code change that materially changes architecture, behavior, interfaces, risks, or project state must update the affected canonical documentation in the same change.

Stale documentation is a project defect.
