# TEST MATRIX — FixHUA

> 05A §9: traceable `requirement → component → mechanism → test → expected result → evidence`. Goal: sufficient evidence for claims made — not maximum count. D-015: device rows are READY plans, not results.

## Legend

- Test classes: UNIT, CONTRACT (interface), INTEGRATION, MIGRATION, FAILURE_INJECTION, REGRESSION, BUILD, INSTALL_SMOKE, DEVICE, PERFORMANCE, SAFETY.
- Evidence states: PLANNED (no code yet) / harness READY / PASSED / FAILED.

## Matrix

| Requirement chain | Test ID | Class | Test | Expected result | Evidence state |
|---|---|---|---|---|---|
| G1 ← D-006/D-007 ← CS-1 uclamp boost ← FX-ACT/POLICY | T-E02 | DEVICE+PERFORMANCE | E-02 A/B launch latency harness | ≥10% median launch latency ↓, no freq overshoot after cap | harness READY (device needed) |
| G3/G4 ← D-013 ← CS-3 max_cached_processes ← FX-ACT | T-E04 | DEVICE | E-04 N-app cycle reload ratio | reloads ↓ ≥25% at +8 with PSI stall unchanged | harness READY |
| G5 ← CS-4 freezer alignment ← FX-ACT | T-E05 | DEVICE | E-05 frozen vs non-frozen resume | frozen ≪ cold start; informs keep-warm sizing | harness READY |
| H-01 ← PSI triggers ← FX-OBS | T-E01 | DEVICE | E-01 trigger fidelity vs allocstall | trigger precedes allocstall spike ≥100 ms moderate ramp | harness READY |
| INV-8 telemetry budget ← FX-OBS | T-E10 | DEVICE+PERFORMANCE | E-10 24 h overhead | <1% avg CPU; ~0 wakelocks | harness READY |
| H-05 ← thermal trend ← FX-OBS/MODEL | T-E08 | DEVICE | E-08 lead-time runs | ≥10 s consistent lead, else CS-8 demotion | harness READY |
| H-06 ← prediction ← FX-MODEL | T-E09 | DEVICE (offline data) | E-09 accuracy harness on FB-1 data | Accuracy@3 ≥60% with abstention; regret < bound | harness PROPOSED (needs FB-1 data) |
| U-005 ← vendor variance ← FX-OBS | T-E12 | DEVICE | E-12 capability detection matrix | ≥80% per-app states detectable | harness READY |
| INV-3 schema boundary ← FX-ACT helper | T-SCHEMA | UNIT+CONTRACT | malformed/out-of-bounds/unknown-version commands | 100% rejected; no side effects | PLANNED |
| IF-05 auth boundary ← fxhelperd | T-AUTH | UNIT+FAILURE_INJECTION | wrong token, wrong UID, replayed frame | all refused; burst → actuation shutdown (RSK-107) | PLANNED |
| FR-06 partial write ← FX-ACT | T-PARTIAL | FAILURE_INJECTION | kill helper mid-write (rigged build) | auto-rollback; journal consistent | PLANNED |
| FR-04/FR-13 corruption ← FX-STORE | T-CORRUPT | FAILURE_INJECTION+MIGRATION | flip bytes per object; cross-version upgrades | per-object behavior per DATA_STATE_MODEL; no crash-loop | PLANNED |
| INV-10 oscillation ← FX-SAFE | T-OSC | FAILURE_INJECTION (sim) | synthetic flip storm on a surface | freeze + stock restore + alert within guard window | PLANNED |
| INV-9 energy budget ← FX-SAFE | T-ENERGY | UNIT (model) + DEVICE (later) | budget breach injection | auto-degrade to OBSERVATION_ONLY | PLANNED |
| Region logic ← FX-POLICY | T-REGION | UNIT | region transition matrix incl. hysteresis/dwell edges | expected transitions only; no flapping | PLANNED |
| Trace replay ← FX-POLICY/MODEL | T-REPLAY | INTEGRATION (sim) | recorded synthetic OBS trace → intent sequence | matches golden intents | PLANNED |
| Journal integrity ← FX-SAFE/STORE | T-JOURNAL | UNIT | append-only + hash chain verification | tamper/gap detected | PLANNED |
| Consent flows ← FX-UI | T-CONSENT | UI/INTEGRATION | tier changes, kill switch propagation ≤1 cycle | actuation halts; journal CONSENT entries | PLANNED |
| Build integrity ← repo | T-BUILD | BUILD | clean rebuild from checkout (§11 runbook) | builds; schema validation passes | PLANNED |
| Install smoke ← APK | T-INSTALL | INSTALL_SMOKE | install on bench device; first-run validation (FIRST_BUILD_CRITICAL_PATH exit criteria) | diagnosis screen renders; probes journal; self-overhead check ok | PLANNED |
| Launch/jank regression ← whole | T-REG | REGRESSION (device) | scripted session stock vs FixHUA, weekly on bench | no SLO regression beyond noise | harness PROPOSED |
| CS-9 lmkd retune ← EXPERIMENTAL | T-E11 | DEVICE (canary) | E-11 supervised canary | pass gates or stay EXPERIMENTAL-locked | harness READY; gated HIGH risk |

## Minimum sufficient tests per build tier

- **FB-1 exit:** T-BUILD, T-INSTALL, T-SCHEMA(read-path subset), T-JOURNAL, plus device probes T-E01/T-E12 readiness (run when device available).
- **FB-2 exit:** + T-REGION, T-REPLAY, T-OSC, T-AUTH, T-PARTIAL, T-CONSENT; device T-E02/T-E04 before any PRODUCTION promotion.
- **FB-3 exit:** + T-E09 loop, T-REG baseline, T-ENERGY device arm.
