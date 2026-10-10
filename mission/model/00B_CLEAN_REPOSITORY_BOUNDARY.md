# CLEAN REPOSITORY BOUNDARY
## Exact execution scope for Blind Genesis V10.3

This mission is bound to one dedicated clean-room GitHub repository.

### Authorized repository
- Full name: `ahaggag037/research-system-brain`
- Repository ID: `1412675689`
- Default branch: `main`
- Intended execution branch: `main`
- Visibility: public

These repository facts were verified before mission packaging.

---

# 1. Hard repository boundary

All mission work must occur inside:

`ahaggag037/research-system-brain`

Do not read, inspect, search, clone, fetch, browse, compare against, or write to any other user repository during Blind Genesis.

This includes but is not limited to:
- `FixHUA`
- old research repositories
- old implementation repositories
- old prompts/branches
- unrelated Android projects

The purpose is to preserve independent invention.

If another repository appears relevant, record the temptation/need as a note and continue without opening it.

---

# 2. Public external research remains allowed

The repository boundary does **not** prohibit independent public research.

You may research:
- Android/AOSP;
- Linux;
- Huawei public documentation;
- SoC/vendor public materials;
- academic papers;
- open-source projects not belonging to the user's prior FixHUA work;
- technical documentation;
- relevant public engineering discussions.

Do not use public search as a backdoor to retrieve the user's prior FixHUA work or cached copies of it.

---

# 3. Repository is the canonical mission workspace

Persist consequential mission state in the authorized repository as work progresses.

At minimum, repository state must eventually contain:
- root `AGENTS.md`;
- canonical project entry document;
- machine-readable project state;
- architecture/decision records;
- component contracts;
- implementation map;
- build/test/runbook knowledge;
- visual twin source;
- final audit;
- handoff checkpoint.

Conversation history is not canonical.

The final GitHub repository is the primary continuation surface.
The ZIP is a transport snapshot of that state.

---

# 4. Working memory location

Keep canonical operational scratch memory inside the authorized repository.

Recommended location:

`.v10/working_memory/`

This is operational state, not final product documentation.

At DESIGN_CONVERGED and again before final handoff:
- distill important knowledge into canonical docs;
- keep only useful recovery/history notes;
- clearly mark scratch/noncanonical material.

Do not place secrets, tokens, signing keys, passwords, or sensitive personal/device information in the repository.

The repository is public.

---

# 5. Commit/checkpoint discipline

If repository write/commit capability is available, persist meaningful checkpoints rather than leaving critical knowledge only in transient context.

Recommended checkpoints:
1. mission initialization / requirement ledger established;
2. major architecture pivot when recovery would otherwise be difficult;
3. DESIGN_CONVERGED;
4. knowledge package substantially complete;
5. final audit repaired and passed;
6. final handoff state.

Do not create commits merely to satisfy a count.

Commit quality matters more than commit quantity.

---

# 6. Branch rule

Use `main` as the canonical branch for this dedicated clean-room repository unless the environment technically requires a temporary branch/PR workflow.

Do not switch to a branch from another repository.
Do not import prior FixHUA branch history.

If a temporary branch is required by tooling, merge or otherwise reconcile the final canonical state back into `main` before final handoff when permitted.

---

# 7. No contamination rule

If you accidentally encounter prior FixHUA architecture or research:
1. stop consuming it;
2. record a contamination event in working memory;
3. identify what was exposed;
4. do not use exposed ideas as evidence;
5. continue from independently established reasoning;
6. disclose the contamination in the final audit.

Do not pretend the blind condition was preserved if it was not.

---

# 8. Repository cold-start requirement

The final repository must pass this exact test:

A capable future model is given only:

> `Open ahaggag037/research-system-brain on GitHub, read the project, and continue from the current state.`

Without access to this conversation, prior FixHUA repositories, or external scratch notes, it must be able to determine:
- what FixHUA/System-Brain research concluded;
- what product/system is actually being proposed;
- what is fact versus hypothesis;
- what has been implemented versus merely specified;
- exact architecture and component responsibilities;
- how components connect;
- how to build/test/validate;
- what remains unknown;
- what the next executable task is;
- what rules protect the system from unsafe or unjustified privileged behavior.

If that is not possible, the repository is not ready for handoff.

---

# 9. Final repository status proof

Before `MISSION_COMPLETE`, record:
- canonical repository full name;
- branch;
- current commit SHA if available;
- package/version ID;
- cold-start audit result;
- number of unresolved blockers;
- next exact milestone.

Do not claim GitHub continuity without this proof.
