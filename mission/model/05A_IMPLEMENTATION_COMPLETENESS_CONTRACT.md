# IMPLEMENTATION COMPLETENESS CONTRACT
## The final knowledge package must explain how the software becomes a real, usable system

This contract exists to prevent a strong architecture from stopping at “implementation-ready” in a vague sense.

The final package must contain enough engineering detail that a capable future coding model can move from repository checkout to a working build without inventing missing architecture.

---

# 1. End-to-end completeness target

The documentation must cover the complete path:

`repository checkout`
→ `environment/prerequisite verification`
→ `project configuration`
→ `module creation`
→ `interface wiring`
→ `runtime startup`
→ `privilege acquisition where applicable`
→ `data flow`
→ `state persistence`
→ `error handling`
→ `recovery`
→ `tests`
→ `build`
→ `signing/release preparation`
→ `installation`
→ `first-run validation`
→ `real-device acceptance`
→ `daily-use readiness`

Do not leave important transitions implied.

---

# 2. Build Runbook

Create a canonical `BUILD_RUNBOOK.md` (or better equivalent) that includes:

- required toolchain;
- required Android SDK/NDK/JDK/Gradle versions when known;
- repository layout;
- environment setup;
- configuration files;
- build variants;
- local build commands;
- CI build path;
- artifact locations;
- signing boundary;
- installation path;
- clean rebuild procedure;
- recovery from common build failures;
- how to verify the produced artifact corresponds to the documented source state.

Do not include secrets or private signing material.

If exact versions are not yet known, mark them `UNKNOWN` and define the smallest verification step.

---

# 3. Codebase Map

Create a canonical code/module map.

For every major module/component include:

- stable ID;
- source path;
- responsibility;
- public interfaces;
- inbound dependencies;
- outbound dependencies;
- runtime owner;
- lifecycle;
- privilege level;
- thread/process expectations;
- persistent state owned;
- logs/telemetry owned;
- failure modes;
- tests;
- diagrams;
- architectural decision references.

A future model should be able to find the correct code area without searching blindly.

---

# 4. Component Contracts

Every major component must have an explicit contract.

Each contract should define:

- purpose;
- inputs;
- outputs;
- preconditions;
- postconditions;
- invariants;
- side effects;
- dependencies;
- failure behavior;
- recovery behavior;
- concurrency assumptions;
- privilege assumptions;
- performance budget if relevant;
- configuration surface;
- test obligations;
- “must not do” constraints.

Do not allow two components to share an important responsibility without documenting ownership.

---

# 5. Interface and Integration Map

Document how components are connected.

For every important connection include:

- caller;
- callee;
- interface/protocol;
- data structure;
- direction;
- sync/async behavior;
- timeout/retry behavior;
- authentication/privilege boundary if any;
- failure propagation;
- rollback behavior;
- observability/logging;
- versioning/migration concerns.

Create at least one end-to-end runtime trace showing a representative user-visible event from UI/input to system effect and back to visible result.

---

# 6. Data and State Model

Document all important state:

- ephemeral runtime state;
- persistent configuration;
- learned/adaptive state if any;
- experiment state;
- safety/rollback state;
- version/migration state;
- device capability state;
- audit/history state.

For each state object define:

- owner;
- schema;
- lifecycle;
- persistence location;
- migration rule;
- corruption behavior;
- reset behavior;
- backup/restore behavior if applicable.

---

# 7. Startup and Shutdown

Document exact lifecycle:

- app start;
- privileged-service start if applicable;
- boot behavior if applicable;
- dependency readiness;
- failure-to-start behavior;
- graceful shutdown;
- crash recovery;
- update/restart behavior.

Do not leave boot-time behavior implicit.

---

# 8. Failure and Recovery Matrix

Create a matrix covering major failures:

- missing privilege;
- missing device capability;
- stale configuration;
- invalid state;
- failed intervention;
- partial intervention;
- daemon/service crash;
- app crash;
- reboot during work;
- thermal/stability regression;
- incompatible firmware change;
- failed update;
- corrupted local state.

For each define:
- detection;
- user-visible effect;
- automatic response;
- rollback;
- degraded mode;
- human action if required;
- verification after recovery.

---

# 9. Test Matrix

Create a traceable test matrix.

Map:

`requirement → component → mechanism → test → expected result → evidence`

Include where relevant:
- unit tests;
- interface/contract tests;
- integration tests;
- state migration tests;
- failure-injection tests;
- regression tests;
- build tests;
- installation smoke tests;
- real-device tests;
- performance validation;
- safety/rollback validation.

The goal is not maximum test count.
The goal is sufficient evidence for the claims being made.

---

# 10. Definition of Ready states

Define objective criteria for:

- `ARCHITECTURE_READY`
- `IMPLEMENTATION_READY`
- `BUILD_READY`
- `DEVICE_TEST_READY`
- `DAILY_USE_READY`
- `RELEASE_READY`

Do not use “ready” as a vague adjective.

Each state must have measurable entry criteria.

---

# 11. First-build critical path

Create a file such as `FIRST_BUILD_CRITICAL_PATH.md`.

It must identify:

- the minimum set of components required;
- exact build order;
- dependencies;
- what can be developed in parallel;
- what may be stubbed temporarily;
- what may not be stubbed;
- mandatory tests before proceeding;
- blocking unknowns;
- exit criteria for the first installable build.

The critical path should be optimized for hours-to-first-use without losing architectural integrity.

---

# 12. Integration proof

Before calling the package implementation-complete, perform a documentation-level integration proof:

For each major user-visible flow, walk through:
- initiating event;
- component sequence;
- state changes;
- privileged operations if any;
- measurements;
- failure branches;
- recovery;
- final visible outcome.

If the walk-through contains an unexplained jump, the implementation documentation is incomplete.

---

# 13. No fake 100%

Do not claim “100% ready” when device-specific evidence is missing.

Instead report one of:

- `FULLY_SPECIFIED`
- `FULLY_IMPLEMENTED`
- `FULLY_TESTED_ON_TARGET`
- `BLOCKED_BY_DEVICE_EVIDENCE`
- `BLOCKED_BY_TOOLING`
- `PARTIALLY_IMPLEMENTED`

The package must make the difference obvious.

A precise limitation is better than false completeness.
