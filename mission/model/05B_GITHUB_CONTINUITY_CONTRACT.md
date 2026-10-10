# GITHUB REPOSITORY CONTINUITY CONTRACT

Canonical repository: `ahaggag037/research-system-brain` (`main`).
## A future model must be able to continue from GitHub alone

The final repository must contain all consequential project memory needed for continuation.

Conversation history must not be required.

---

# 1. Root-level discovery

The repository root must contain a short, obvious AI/engineer entry point.

Preferred:
- `AGENTS.md`

Optionally also:
- `AI_START_HERE.md`

`README.md` should link to the canonical project entry point if doing so is appropriate.

A future model receiving only:

> “Read the project from GitHub and continue.”

should be able to discover the correct starting file without hidden chat context.

---

# 2. Root AGENTS.md requirements

`AGENTS.md` must be concise and include:

1. canonical first document to read;
2. current project phase;
3. location of machine-readable project state;
4. location of architecture decisions;
5. location of component contracts;
6. location of build runbook;
7. location of test/validation matrix;
8. location of diagrams/visual twin;
9. truth-state rules;
10. documentation-update rule;
11. rule for privileged changes;
12. rule for contradictions;
13. instruction not to guess missing device facts.

---

# 3. Canonical project entry document

Create a canonical document such as:

`docs/project/00_START_HERE.md`

It should answer in a few minutes:

- what FixHUA is;
- what problem it solves;
- what architecture is selected;
- what is implemented;
- what is not implemented;
- what is verified on device;
- what is unknown;
- what the next milestone is;
- where to read for each subsystem;
- what invariants must not be violated.

---

# 4. Machine-readable navigation

Create:
- `PROJECT_STATE.json` or equivalent;
- `KNOWLEDGE_GRAPH.json` or equivalent;
- `IMPLEMENTATION_MAP.json` or equivalent;
- `VISUAL_TRACEABILITY.json` or equivalent when visuals exist.

A future model should be able to navigate the repository programmatically.

---

# 5. Implementation Map

The implementation map should link:

`requirement`
→ `architecture decision`
→ `mechanism`
→ `component`
→ `source path`
→ `interface`
→ `test`
→ `diagram`
→ `status`

This map is the bridge between documentation and actual code.

---

# 6. Repository truth hierarchy

Define a conflict-resolution order.

For “what currently runs,” prefer:
1. verified device evidence;
2. current checked-out code;
3. current tests/CI;
4. current build/release artifacts;
5. current project state/documentation.

For “why the architecture is designed this way,” prefer:
1. active architectural decision records;
2. canonical architecture document;
3. component contracts;
4. implementation blueprint.

If these disagree, do not guess.
Record and repair the contradiction.

---

# 7. Same-change documentation rule

A code change that alters:
- architecture,
- interfaces,
- behavior,
- privilege requirements,
- safety properties,
- state schema,
- build procedure,
- or project phase

must update the affected canonical documentation and machine-readable state in the same change.

Documentation drift is a defect.

---

# 8. Handoff checkpoint

Before final packaging, create a handoff checkpoint containing:

- current architecture ID/version;
- current branch/commit if available;
- current build state;
- current test state;
- current device-validation state;
- active blockers;
- top risks;
- next exact task;
- files the next model must read first.

---

# 9. GitHub cold-start acceptance

Perform this test:

Assume the next model has:
- repository access,
- no conversation history,
- no external notes,
- no memory of this mission.

Give it only the instruction:

> “Read the project from GitHub and continue from the current state.”

Using only repository artifacts, it should be possible to determine:

- what to read first;
- what is true now;
- what is proposed;
- what to build next;
- where the relevant code belongs;
- what tests are required;
- what risks/invariants apply;
- how to build;
- how to validate;
- how to update project state.

If any answer requires hidden context, the repository handoff is incomplete.

---

# 10. No hidden critical knowledge

Consequential knowledge must not live only in:
- chat history;
- temporary scratch notes;
- a single model’s transient context;
- an unavailable plugin;
- an unlinked diagram;
- an unreferenced external document.

If it matters for correct continuation, it belongs in the repository knowledge system.
