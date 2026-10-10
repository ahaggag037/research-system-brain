# Component contract — FX-MODEL

- **Stable ID:** FX-MODEL
- **Proposed source path:** `app/model/` (state estimation + prediction) — PROPOSED, not implemented
- **Runtime owner:** app process
- **Lifecycle:** lives with app service; models persisted via FX-STORE; retrained online with bounded updates
- **Diagrams:** `../visuals/COMPONENT_RELATIONS.mmd`, `RUNTIME_FLOW.mmd`
- **Decision references:** D-012 (low-regret arming), D-015

## Contract

- **Purpose:** estimate current device/workload state (pressure regime, interaction phase, thermal trend) and predict near-future app usage with calibrated confidence and abstention.
- **Inputs:** FX-OBS event stream; FX-STORE learned state (Markov transition tables, time/context features); explicit user context signals (screen on/off, unlock) via framework callbacks.
- **Outputs:** `StateEstimate` (region labels + confidence) to FX-POLICY; `PredictionSet` {candidate apps, p-values, confidence, abstain flag} to FX-POLICY; online accuracy/regret metrics to FX-STORE.
- **Preconditions:** OBS stream active; for prediction: ≥ minimum usage history (until then: abstain; heuristics only).
- **Postconditions:** every emitted prediction carries confidence + abstention decision; state estimate freshness < 2× sampling period.
- **Invariants:** never emits unbounded-confidence predictions; abstains below data thresholds; never proposes actions (that is POLICY's job); prediction can arm only the low-regret action set (D-012).
- **Side effects:** none on device surfaces; compute bounded.
- **Dependencies:** FX-OBS, FX-STORE.
- **Failure behavior:** model corruption → reset to priors + abstain (DATA_STATE_MODEL §learned state); prediction disabled flag → heuristics mode.
- **Recovery behavior:** model resettable from UI; versioning triggers migration or reset (never silent carry-over of incompatible learned state).
- **Concurrency assumptions:** single model-update thread; lock-free read snapshots for POLICY.
- **Privilege assumptions:** none.
- **Performance budget:** update ≤ a few ms/event on mid-tier SoC; memory < 16 MB models.
- **Configuration surface:** feature toggles, confidence thresholds (with safe defaults; raising allowed, lowering below floor refused), reset.
- **Test obligations:** unit: estimator transitions, calibration; offline: E-09 accuracy harness on FB-1-collected data; regression: accuracy@3 vs baseline recency heuristic.
- **Must not do:** learn during anomalous states flagged by SAFE (freeze periods); export raw usage data off device; arm any action outside the low-regret set; claim accuracy without the E-09 harness numbers.
