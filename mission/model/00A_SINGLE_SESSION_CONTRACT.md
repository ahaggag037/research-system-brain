# SINGLE-SESSION EXECUTION CONTRACT
## Mandatory continuity rule

This mission must be executed as one continuous work session from initialization through final transfer packaging.

The intent is not “finish one phase and ask whether to continue.”
The intent is:

`START → DISCOVER → CONVERGE → PACKAGE → VISUALIZE → AUDIT → ZIP → HANDOFF`

without voluntary interruption.

---

# 1. Start immediately

After reading the Control Plane and required initialization files:

- do not ask for permission to begin;
- do not ask whether to continue to the next phase;
- do not stop after producing a plan;
- do not stop after research;
- do not stop after architecture selection;
- do not stop after documentation;
- do not stop after diagrams;
- do not stop before the final audit;
- do not stop before attempting to create the final ZIP.

Proceed autonomously through the entire mission.

---

# 2. No confirmation gates

Transitions between mission states are automatic when their criteria are satisfied.

Do not require user confirmation for:

- entering Discovery;
- changing architecture;
- declaring DESIGN_CONVERGED;
- entering Knowledge Packaging;
- creating the Visual Twin;
- entering Final Audit;
- repairing audit failures;
- creating the transfer package.

The mission contract itself is the authorization to continue.

---

# 3. No artificial pauses

Do not voluntarily pause because:

- a phase is complete;
- the work is long;
- the output is large;
- you found a good architecture;
- a document is finished;
- a tool call completed;
- another model might normally ask for confirmation.

Continue directly to the next highest-value required action.

Do not respond with “I can continue,” “tell me to continue,” “next I will…,” or equivalent progress-only handoffs when the mission can still proceed.

---

# 4. Blocking conditions

A blocker is valid only when progress on a mandatory requirement genuinely depends on something unavailable in the current environment, such as:

- an unavailable tool/capability;
- missing device evidence that cannot be obtained;
- a required external credential that is not present;
- a hard platform restriction;
- an inaccessible artifact that cannot be reconstructed.

When a blocker occurs:

1. record it in the Requirement Ledger and Checkpoint;
2. identify the smallest recovery action;
3. continue every other independent part of the mission;
4. do not stop the whole mission unless the blocker makes all remaining work impossible.

A missing convenience is not a blocker.

---

# 5. Tool failure resilience

If a tool fails:

- retry only when a retry is reasonable;
- use a safe alternative when available;
- record the limitation;
- continue with unaffected work.

Do not abandon the mission because one optional tool, connector, diagram service, or plugin is unavailable.

Example:
if Figma is unavailable, use an editable repository-friendly diagram format and continue.

---

# 6. One session does not mean one giant response

You may use as many tool calls, internal files, checkpoints, and intermediate artifacts as needed.

The requirement is continuity of execution, not compression into one message.

Do not sacrifice research depth or package quality merely to fit everything into one response.

---

# 7. Context-continuity rule

Because the session may be long:

- maintain Working Memory continuously;
- checkpoint before context-heavy transitions;
- distill notes when they become noisy;
- recover from canonical working-memory files if context becomes crowded.

Never use “the task is long” as a reason to stop.

---

# 8. Self-repair loop

If the Final Audit finds defects:

`AUDIT FAILURE → REPAIR → RE-AUDIT`

Repeat within the same session until:

- all mandatory requirements are satisfied, or
- remaining mandatory failures are genuinely blocked and explicitly documented.

Do not hand off a package merely because the first audit failed.

---

# 9. Finish condition

The continuous session ends only when one of these states is reached:

## A. MISSION_COMPLETE

All mandatory requirements are:
- satisfied, or
- explicitly blocked with precise evidence and recovery path,

and:
- the cold-start audit is complete;
- the visual/document consistency audit is complete;
- the transfer package has been created if tooling permits;
- the final handoff has been produced.

## B. MISSION_INCOMPLETE_HARD_BLOCK

Use only when an external hard blocker makes meaningful continuation impossible.

If used, provide:
- exact blocker;
- work completed;
- work remaining;
- recovery action;
- all artifacts produced so far.

Do not use this state simply because the session is long or difficult.

---

# 10. Final continuity principle

**Do not stop because you reached a milestone. Stop only because the mission reached its terminal state.**
