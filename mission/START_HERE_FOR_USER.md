# FixHUA Blind Genesis V10 — How to use this package

Upload this ZIP/folder to the target model in a **clean conversation/workspace**.

Do not give it prior FixHUA research, old prompts, old architecture notes, or our earlier design discussion.

Tell the model only:

> Work exclusively inside GitHub repository `ahaggag037/research-system-brain` on `main`. Open `model/00_CONTROL_PLANE.md` from the supplied V10.3 mission package and follow it exactly. Treat the V10.3 package as the complete mission contract. Do not inspect `FixHUA` or any other prior user repository, branch, prompt, research note, architecture, or implementation. Independent public research is allowed. Execute the entire mission in one continuous session without asking me to confirm continuation between phases. Persist consequential state into `research-system-brain` as you work. Do not stop at intermediate milestones. Continue through final audit, GitHub cold-start validation, and transfer ZIP creation unless a genuine hard external blocker makes meaningful continuation impossible. Do not read `_human/`.

The model should progressively load the other files only when the Control Plane tells it to.

## Why this package is modular

V10 deliberately avoids one huge prompt. The model receives:
- freedom during discovery,
- a separate working-memory system,
- a separate compliance layer,
- packaging rules only after the design converges.

This reduces instruction overload while keeping the mission strict.

## Expected final artifact

The target model should eventually create:

`FixHUA-Blind-Genesis-V10-Knowledge-Package.zip`

and return its exact path/artifact.

If its environment cannot create ZIP files, it must say so honestly and provide the exact intended package tree instead.
