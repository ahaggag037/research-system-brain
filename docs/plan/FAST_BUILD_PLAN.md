# FAST BUILD PLAN — three builds

> 05 §8: dedicated shortest-path plan to (1) first useful build, (2) first installable/testable build, (3) first daily-usable build. Speed through sequencing, not superficiality. Cross-ref: stage blueprints in `IMPLEMENTATION_BLUEPRINT.md` §B.

## Build 1 — First useful build (= FB-1 core)

- **Target:** user installs, immediately sees *why* their device stutters/kills (pressure timeline, kill events, vendor policy exposure). Useful even with zero actuation.
- **Critical path:** schemas → store → OBS baseline → diagnosis UI. (Same as FIRST_BUILD_CRITICAL_PATH.)
- **Parallel work:** bench harness scripts; UI polish; probe executor.
- **Blockers:** none known pre-device; toolchain version resolution (small).
- **Minimum sufficient tests:** T-BUILD, T-JOURNAL, schema units.
- **Intentionally deferred:** actuation, prediction, Perfetto auto-sessions, helper.
- **Expected evidence before moving on:** FB-1 exit criteria met; E-10 self-check <1% CPU.

## Build 2 — First installable/testable build (= FB-1 complete + FB-2 shadow)

- **Target:** bench-installable build that can *run experiments*: probes + shadow policy (intents logged, not executed) + optionally helper in read-only mode.
- **Critical path:** FB-1 exit → POLICY region engine v1 → SAFE journal/governor → shadow mode → bench harness integration (E-01/E-03/E-05/E-10/E-12 executable).
- **Parallel work:** helper implementation + security review ∥ policy engine ∥ harnesses.
- **Blockers:** helper security review (RSK-107); bench device availability (BLOCKED_BY_DEVICE_EVIDENCE currently).
- **Minimum sufficient tests:** + T-REGION, T-REPLAY, T-SCHEMA, T-AUTH, T-CONSENT full.
- **Intentionally deferred:** live actuation (needs E-02/E-04 gates), prediction arming, CS-9.
- **Expected evidence:** E-01/E-03/E-05/E-10/E-12 verdicts recorded; shadow intent log sanity (no absurd intents on replay).

## Build 3 — First daily-usable build (= FB-3)

- **Target:** consent-gated actuation on PRODUCTION-admitted surfaces; prediction-armed low-regret actions; soak-stable.
- **Critical path:** FB-2 exit → E-02/E-04 PASS → CS-1/CS-3/CS-4 promotion → MODEL arming after E-09 → CS-5/6/7/8 after E-06/07/08/09 → 7-day soak → DAILY_USE_READY review.
- **Parallel work:** model training pipeline ∥ adapters ∥ soak automation.
- **Blockers:** every E-series gate; energy budget validation.
- **Minimum sufficient tests:** full TEST_MATRIX minus EXPERIMENTAL rows.
- **Intentionally deferred:** CS-9 (EXPERIMENTAL track S4), ARCH-C anything.
- **Expected evidence:** SLO deltas vs stock measured (MEASUREMENT_STRATEGY); zero FR-10 events in soak; DAILY_USE_READY checklist complete.

## Sequencing logic (why this order is fastest)

1. Schemas first: they are the cheapest artifact and unblock all parallel tracks (UI/store/collectors/harness).
2. Observation before actuation (D-008): FB-1 *is* the device-truth discovery step (U-003/U-005) — skipping it doesn't save time, it blinds FB-2.
3. Shadow before canary: policy bugs cost nothing when intents aren't executed; the same code path then serves canary.
4. Promotion only through evidence gates: avoids rework from over-eager actuation (RSK-101/103/106 discovered cheaply in shadow/canary).
