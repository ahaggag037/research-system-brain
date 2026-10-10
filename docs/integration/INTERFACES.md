# INTERFACES — interface and integration map

> Canonical home for component connections (05A §5). End-to-end traces: `RUNTIME_TRACES.md`. All schemas PROPOSED (no code exists); JSON schema files planned under `schemas/`.

## Connection map

### IF-01 FX-OBS → FX-MODEL: telemetry event stream
- Caller: FX-OBS | Callee: FX-MODEL
- Interface/protocol: in-process event bus (callback subscription), `ObsEvent` schema
- Data structure: `ObsEvent { ts_ns, class: PSI_TRIGGER|COUNTER|LIFECYCLE|VENDOR_POLICY|THERMAL|PROBE, payload: variant, confidence }`
- Direction: OBS→MODEL | Sync/async: async, ordered per class
- Timeout/retry: n/a in-process; queue bound 4096 events, overflow → drop oldest COUNTER class + count
- Auth/privilege boundary: none (same process)
- Failure propagation: MODEL detects sequence gaps via monotonous seq; gap → state estimate freshness decay
- Rollback: none (telemetry is immutable facts)
- Observability: drop counters in journal
- Versioning: schema `obs_event.v1`; additive-only changes; breaking change = new version id

### IF-02 FX-MODEL → FX-POLICY: estimates + predictions
- Caller: FX-MODEL | Callee: FX-POLICY
- Protocol: in-process snapshot read (lock-free) + change notification
- Data: `StateEstimate { pressure_regime, interaction_phase, thermal_trend, freshness_ms, confidence }`; `PredictionSet { candidates[3], p[], confidence, abstain }`
- Direction: MODEL→POLICY | async notify + pull snapshot
- Timeout/retry: POLICY treats stale (>2× period) as absent → hold region
- Auth: none | Failure propagation: absent input = hold, never invent
- Rollback: n/a | Observability: estimate age metric | Versioning: `model.v1`

### IF-03 FX-POLICY → FX-SAFE: action intents
- Caller: FX-POLICY | Callee: FX-SAFE
- Protocol: in-process queue
- Data: `ActionIntent { id, surface: CS-*, params (typed, bounded), ttl_ms, region_justification, expected_slo_class, energy_estimate }`
- Direction: POLICY→SAFE | sync handoff, async execution
- Timeout: intents expire at ttl | Retry: none (policy re-decides next cycle)
- Auth: none | Failure propagation: rejection → journal + policy sees outcome next cycle
- Rollback: SAFE may initiate rollback intents | Observability: decision log | Versioning: `intent.v1`

### IF-04 FX-SAFE → FX-ACT (client): admitted intents
- Caller: FX-SAFE | Callee: FX-ACT client
- Protocol: in-process call
- Data: admitted `ActionIntent` + admission proof {surface state ≥ required, consent tier}
- Direction: SAFE→ACT | sync admission, async execution
- Timeout/retry: ACT reports result; SAFE does not retry blindly (policy re-decides)
- Auth: none in-process | Failure propagation: ACT error codes → SAFE governor (may freeze surface)
- Rollback: SAFE can issue rollback intent referencing journal entry
- Observability: journal both legs | Versioning: `intent.v1`

### IF-05 FX-ACT client → `fxhelperd`: privileged command socket
- Caller: FX-ACT client | Callee: `fxhelperd` (root daemon)
- Protocol: length-prefixed binary frames on abstract-namespace UNIX socket; command schema `helper/schema/fxhelperd.v1.json` (PROPOSED)
- Data: `Cmd { op: UCLAMP_SET|ADJ_SET|VM_SET|IO_SET|WARM_PAGES|LMKD_RETUNE|READBACK|STOCK_SNAPSHOT|PROBE, target, params, intent_id }` → `Res { ok, before, after, error_code }`
- Direction: request/response | sync with 2 s timeout
- Timeout/retry: timeout → fail-closed error; no auto-retry of writes
- Auth/privilege boundary: **the** privilege boundary — per-install token + peer-UID check; helper validates schema + bounds again (defense in depth)
- Failure propagation: typed error codes; partial write → helper auto-rollback attempt before responding
- Rollback: `STOCK_SNAPSHOT` values + journal before-values
- Observability: helper logs to journal via client callback
- Versioning: schema version negotiated at handshake; mismatch → refuse

### IF-06 FX-ACT/SAFE/… → FX-STORE: state persistence
- Caller: all components | Callee: FX-STORE
- Protocol: in-process DAO (Room/DataStore class)
- Data: per-object schemas (`DATA_STATE_MODEL.md`)
- Direction: bidirectional | sync reads (snapshots), async batched writes (journal flush ≤1 Hz)
- Timeout/retry: write retry with backoff; journal write failure → SAFE blocks actuation (fail-closed)
- Auth: none in-process | Failure propagation: corruption behavior per object (DATA_STATE_MODEL §6)
- Rollback: config snapshot restore | Observability: write latency metrics | Versioning: per-object schema versions + migrations

### IF-07 FX-UI ↔ FX-STORE/FX-SAFE/FX-OBS
- Caller: FX-UI | Callees: STORE (reads), SAFE (consent/kill switch), OBS (diagnostic session request)
- Protocol: in-process state flows + commands
- Data: consent tier, journal queries, diagnostic bundles, safety state
- Direction: bidirectional | async flows
- Timeout/retry: UI shows stale badge on timeout | Auth: none
- Failure propagation: store unavailable → cached snapshot + banner
- Rollback: consent downgrade instant | Observability: UI events (local only) | Versioning: follows object schemas

### IF-08 FX-OBS → Perfetto (diagnostic)
- Caller: FX-OBS | Callee: platform Perfetto service
- Protocol: `perfetto` cmdline / TraceConfig API (S-021)
- Data: trace config (ftrace, process_stats, android.surfaceflinger.frametimeline) → trace file
- Direction: request/artifact | async session, time-boxed ≤ 60 s
- Timeout/retry: session abort on timeout | Auth: user-initiated only
- Failure propagation: trace failure → UI error, no retry storm
- Rollback: delete partial trace | Observability: session journal entry | Versioning: config template versioned

## Integration invariants

1. FX-POLICY never calls FX-ACT directly (all actuation via FX-SAFE).
2. FX-OBS never receives write-capable handles.
3. The UNIX socket (IF-05) is the only cross-privilege channel; no other root path exists.
4. Journal (IF-06) is append-only; no component may update/delete journal rows.
5. Every cross-component data structure is schema-versioned; unknown versions fail closed.
