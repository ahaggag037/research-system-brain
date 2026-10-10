# WORKING MEMORY PROTOCOL
## Externalize consequential state so it is not lost

Transient context is not a reliable place for important mission state.

If forgetting something could materially damage the mission, externalize it.

---

## 1. Canonical working memory

Create a `working_memory/` directory using the templates in this package.

Minimum files:
- `REQUIREMENT_LEDGER.md`
- `ACTIVE_NOTES.md`
- `CHECKPOINT.md`
- `DECISION_LOG.md`
- `EXPERIMENT_REGISTRY.md`
- `SOURCE_INDEX.md`
- `CONTRADICTION_REGISTER.md`
- `RISK_REGISTER.md`

These files are operational scratch state, not final project documentation.

---

## 2. Optional plugins/connectors

You may use any suitable connected notes/workspace tool as an additional memory surface if useful.

But:
- do not make mission continuity depend on one external plugin;
- keep canonical recoverable state in the mission workspace when possible;
- do not store secrets, private keys, passwords, tokens, or sensitive personal/device data in scratch notes.

Do not describe these notes as guaranteed private from the user or service.

---

## 3. Write events

Update working memory when any of these occurs:

- important fact discovered;
- important source found;
- new high-potential idea;
- major hypothesis changed;
- architecture changed;
- decision superseded;
- contradiction discovered;
- requirement satisfied/blocked;
- experiment planned/completed;
- risk classification changed;
- tool limitation affects the plan;
- next-step ordering changes.

Do not rely on “I will remember this.”

---

## 4. Checkpoint events

Create/update `CHECKPOINT.md`:
- after major architecture changes;
- before leaving a long research branch;
- after consequential experiments;
- before a tool-heavy or context-heavy branch;
- before switching mission states;
- before final packaging;
- whenever context feels crowded or continuity risk rises.

A checkpoint should let the mission recover without replaying the entire conversation.

---

## 5. Recovery procedure

After interruption, handoff, restart, or context loss:

1. read `CHECKPOINT.md`;
2. read `REQUIREMENT_LEDGER.md`;
3. read `DECISION_LOG.md`;
4. read only the additional working-memory files needed for the current task;
5. reconcile against actual artifacts/source files before continuing.

Do not assume a checkpoint is correct if the workspace contradicts it.

---

## 6. Memory hygiene / garbage collection

Working memory must remain useful.

When notes become repetitive or hard to scan:
- merge duplicates;
- move obsolete items to an archive section;
- preserve IDs and provenance;
- mark superseded decisions rather than deleting them;
- keep `ACTIVE_NOTES.md` focused on what can still change future action.

Do not solve forgetting by creating an unreadable pile of notes.

---

## 7. Distillation

At `DESIGN_CONVERGED`, do not blindly copy scratch notes into final documentation.

Distill:
- validated facts,
- final decisions,
- important rejected alternatives,
- unresolved uncertainties,
- evidence,
- and implementation implications

into the final Knowledge Package.

Scratch notes remain evidence of process, not the canonical architecture.

---

## 8. Privacy and reasoning boundary

Store concise operational information.

Do not attempt to externalize private hidden chain-of-thought.

Useful note:
> “Hypothesis H-07 weakened after source S-12; need experiment E-03.”

Unnecessary note:
> a long verbatim transcript of internal reasoning.
