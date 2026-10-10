# DEFINITION OF READY — objective entry criteria

> 05A §10. "Ready" is never a vague adjective. Current status per `.v10/state/PROJECT_STATE.json` and `../project/TRUTH_REGISTER.md` §6.

## ARCHITECTURE_READY — ✅ satisfied (evidence below)

All true:
- problem model stable and documented (`docs/project/PROBLEM_MODEL.md`);
- architecture selected with challengers recorded (D-006/D-007; `ALTERNATIVES.md`);
- survived adversarial self-attack (DESIGN_SPACE §5; 10 attacks with mitigations);
- component set with unique responsibilities (`docs/components/FX-*.md`);
- safety invariants published (`SAFETY_INVARIANTS.md`);
- critical unknowns explicit with discovery tests (U-003..U-007 ↔ E-series).

## IMPLEMENTATION_READY — ✅ satisfied as FULLY_SPECIFIED (documentation level)

All true:
- component contracts complete (16 fields each, 7/7);
- interface map + end-to-end traces complete (`INTERFACES.md`, `RUNTIME_TRACES.md`);
- data/state model complete (8 classes, 8 fields each);
- lifecycle documented incl. boot (`STARTUP_SHUTDOWN.md`);
- failure/recovery matrix complete (13 types);
- build runbook + first-build critical path + fast-build plan complete;
- implementation blueprint with per-stage entry/exit criteria complete;
- no fabricated device-specific controls: unverified device details marked UNKNOWN with smallest discovery step.

## BUILD_READY — ⬜ not satisfied

Requires:
- code tree exists per `IMPLEMENTATION_BLUEPRINT.md` module map;
- toolchain versions resolved from UNKNOWN (BUILD_RUNBOOK §1);
- T-BUILD passes on clean checkout;
- helper security review scheduled (RSK-107 gate);
- bench device available for install smoke.

## DEVICE_TEST_READY — ⬜ BLOCKED_BY_DEVICE_EVIDENCE

Requires:
- FB-1 build installable (BUILD_READY);
- bench device (rooted, EMUI-class) available;
- E-01/E-03/E-05/E-10/E-12 harnesses packaged in `bench/`;
- consent/journal UI functional for supervised runs.
Current: harness plans READY; device absent (D-015).

## DAILY_USE_READY — ⬜ BLOCKED_BY_DEVICE_EVIDENCE

Requires:
- E-02, E-04, E-10 PASSED on bench device;
- T-OSC/T-PARTIAL/T-AUTH/T-CORRUPT PASSED;
- 7-day soak on a daily-driver class device with no FR-10 events;
- energy budget never breached; journal review clean;
- truth: SLO deltas measured vs stock (MEASUREMENT_STRATEGY).

## RELEASE_READY — ⬜ BLOCKED_BY_DEVICE_EVIDENCE (+ tooling: signing)

Requires:
- DAILY_USE_READY;
- all OPEN risks closed or explicitly accepted with mitigations shipped;
- release variant contains only PRODUCTION-admitted surfaces;
- signing boundary resolved (user-owned keystore; external to this package);
- update/migration tests pass from previous release.

## Honesty rule (05A §13)

Only labels: FULLY_SPECIFIED / FULLY_IMPLEMENTED / FULLY_TESTED_ON_TARGET / BLOCKED_BY_DEVICE_EVIDENCE / BLOCKED_BY_TOOLING / PARTIALLY_IMPLEMENTED. Today: architecture+implementation docs FULLY_SPECIFIED; code 0% (PARTIALLY_IMPLEMENTED does not even apply — nothing implemented); everything device-facing BLOCKED_BY_DEVICE_EVIDENCE. A precise limitation beats false completeness.
