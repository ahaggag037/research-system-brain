# TRANSFER PACKAGE SPEC

Create the final artifact:

`FixHUA-Blind-Genesis-V10.3-Clean-Repo-Knowledge-Package.zip`

---

## Required transfer properties

The ZIP must be:
- self-contained;
- navigable;
- internally consistent;
- explicit about truth states;
- sufficient for cold continuation;
- free of secrets/private keys/passwords/tokens;
- and honest about missing artifacts.

---

## Include

At minimum include:
- canonical entry file;
- final architecture;
- current project state;
- knowledge graph;
- visual twin source files;
- architecture decisions;
- implementation blueprint;
- build runbook;
- code/module map;
- component contracts;
- interface/integration map;
- data/state model;
- startup/shutdown lifecycle;
- failure/recovery matrix;
- test matrix;
- first-build critical path;
- GitHub repository handoff files;
- fast-build plan;
- test/experiment strategy;
- risk-transmutation catalog;
- rejected/deferred idea memory;
- future-model protocol;
- schemas/examples required for implementation;
- final Requirement Traceability Matrix;
- final audit report;
- package manifest.

---

## Manifest

Create a machine-readable manifest containing for each file:
- path;
- purpose;
- status;
- canonical/noncanonical flag;
- size;
- cryptographic hash if tooling permits.

Also include the selected architecture ID/version and package creation timestamp if available.

---

## Artifact integrity

If possible, create a `SHA256SUMS.txt` for the final ZIP contents or equivalent cryptographic manifest.

---

## If ZIP creation is unavailable

Do not claim the ZIP exists.

Provide:
- exact intended directory tree;
- all produced files;
- exact limitation;
- the command/action a capable environment would need to archive it.

---

## Final handoff message

Return a concise handoff containing:
- artifact name/path;
- selected architecture identifier;
- first executable milestone;
- number of unresolved blocking unknowns;
- number of MUST requirements blocked;
- whether the cold-start audit passed;
- whether the package integrity manifest was created.

Then state either:
- `MISSION_COMPLETE`
or
- `MISSION_INCOMPLETE`
