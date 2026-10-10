# RUNTIME TRACES — end-to-end integration proof

> 05A §5 + §12: walk each major user-visible flow end-to-end — initiating event, component sequence, state changes, privileged operations, measurements, failure branches, recovery, final visible outcome. No unexplained jumps. All flows PROPOSED (no code exists); device-dependent effects labeled HYPOTHESIS (D-015).

## TRACE-1 — App launch acceleration (CS-1 uclamp boost)

1. **Initiating event:** user taps app icon; launcher starts activity; framework process-start signals observable via lifecycle callbacks (and UsageStats-class events with grant).
2. **Component sequence:** FX-OBS (lifecycle event `APP_LAUNCHING`, plus PSI trigger state) → FX-MODEL (interaction_phase `idle→launch`; pressure regime from PSI) → FX-POLICY (region `launch` × pressure → intent: CS-1 bounded uclamp min +Δ for ≤2 s on the launching app's cgroup) → FX-SAFE (admission: CS-1 ≥ SAFE on device? consent tier ≥2? dwell/rate OK? energy OK?) → FX-ACT client → `fxhelperd` `UCLAMP_SET` → readback verify.
3. **State changes:** GovernorCounters(CS-1).flips++, energy += estimate; journal INTENT/ADMISSION/ACTUATION/READBACK entries; region timer armed for expiry.
4. **Privileged operations:** one schema write `cpu.uclamp.min` on the app cgroup; expiry write restores baseline (journaled).
5. **Measurements:** OBS records launch latency markers (am display timing where observable), FrameTimeline jank class counts (diagnostic mode), boost duration/freq response (counter class). Hypothesis H-02 target ≥10% median improvement — measured by E-02 harness, not asserted.
6. **Failure branches:** (a) admission state < SAFE → intent rejected, launch proceeds stock; (b) helper unreachable → fail-closed, launch stock; (c) readback mismatch → rollback + surface frozen + UI alert; (d) vendor boost detected on surface (readback shows external writer) → INV-11 defer, journal VENDOR_DEFER.
7. **Recovery:** any abort → baseline restore + surface quarantine; SAFE mode stays NORMAL unless oscillation guard trips.
8. **Final visible outcome:** app launches; if H-02 holds, measurably faster; worst case = stock behavior + one journaled no-op.

## TRACE-2 — Multitasking continuity preservation (CS-3/CS-4 + CS-2)

1. **Initiating event:** user cycles through N apps (multitasking session); background pressure rising (PSI memory `some` trigger crosses).
2. **Sequence:** FX-OBS (PSI trigger event + freeze/lifecycle events) → FX-MODEL (`pressure_regime: calm→rising`; prediction set for likely-return apps) → FX-POLICY (region `rising` × `multitask` → intents: (a) CS-3 max_cached_processes +Δ within memory budget — if admission PRODUCTION; (b) CS-2 timed adj nudges on predicted-return set — if E-03 passed; never kills) → FX-SAFE admission → FX-ACT executes via device_config (CS-3) / helper `ADJ_SET` (CS-2) with readback.
3. **State changes:** capability registry consulted; journal entries; region `rising` dwell timer; memory budget recomputed.
4. **Privileged operations:** device_config put (documented override, S-013); adj writes bounded to nudge range.
5. **Measurements:** reload ratio in scripted N-app cycle (E-04 harness), resume latency (E-05), PSI full-stall watch (regression guard: if full stalls rise → policy reverses to lower region).
6. **Failure branches:** (a) pressure keeps rising → region `critical`: continuity intents stopped, protective stance only (no kills — lmkd remains the killer; FixHUA never outbids it); (b) AMS overwrites adj (expected) → tolerated, re-nudge at next event if still predicted; (c) device_config write rejected (vendor lock) → surface marked BLOCKED in registry, UI guidance offered instead (CS-10 path).
7. **Recovery:** region exit restores device_config to stock snapshot when session ends (if policy requires); adj nudges self-expire via AMS recompute.
8. **Final visible outcome:** fewer reloads of the protected/predicted set (H-04, gated by E-04); worst case = stock kill behavior unchanged.

## TRACE-3 — Pre-thermal-throttle shaping (CS-8, advisory only)

1. **Initiating event:** sustained load (game/video); FX-OBS thermal trend collector sees zone temperature slope + headroom falling (S-005-class signals).
2. **Sequence:** FX-OBS (THERMAL event: trend + headroom estimate) → FX-MODEL (`thermal_trend: warming→hot`, predicted time-to-throttle if estimable — E-08) → FX-POLICY (region `hot` → intent: CS-8 advisory shaping = reduce FixHUA's own boosts, stop pre-warming, suggest background deprio via CS-6 if admitted) → SAFE → ACT.
3. **State changes:** energy budget credited (shaping saves energy); journal entries.
4. **Privileged operations:** none on thermal config (INV-1); only FixHUA's own levers are relaxed; optional CS-6 io weight change (admitted only).
5. **Measurements:** throttle onset time, freq clamp duration, jank during episode (E-08 protocol).
6. **Failure branches:** (a) lead time too short/erratic (H-05 falsified) → CS-8 demoted to reactive smoothing, catalog entry updated; (b) thermal signals unreadable → capability UNKNOWN, flow disabled on device.
7. **Recovery:** none needed (advisory); stock thermal stack untouched by construction.
8. **Final visible outcome:** smoother sustained session if H-05 holds; worst case = stock.

## TRACE-4 — Kill switch (user-visible safety flow)

1. **Initiating event:** user taps "Disable all actuation" in FX-UI (or consent downgrade tier 2→1).
2. **Sequence:** FX-UI → FX-SAFE (kill switch set) → SAFE: mode → OBSERVATION_ONLY; issue rollback intents for all surfaces with non-stock journaled values → FX-ACT executes restores with readback → journal SAFETY + ROLLBACK entries → UI confirms.
3. **State changes:** SafetyState=OBSERVATION_ONLY persisted; GovernorCounters frozen.
4. **Privileged operations:** restore writes only.
5. **Measurements:** readback equals stock snapshot per surface (verification).
6. **Failure branches:** helper unreachable mid-restore → SAFE schedules retry at next start + boot reconciliation (STARTUP_SHUTDOWN §6); UI shows "restore pending".
7. **Recovery:** the flow *is* the recovery.
8. **Final visible outcome:** device at stock behavior; observation continues (INV-4); UI badge shows OBSERVATION_ONLY.

## TRACE-5 — Cold boot reconciliation

Covered in `../lifecycle/STARTUP_SHUTDOWN.md` §6 (boot) and `../ops/FAILURE_RECOVERY_MATRIX.md` FR-09 — journal-vs-live readback audit, stock restore of stale writes, registry reprobe if build fingerprint changed.

## Integration-proof completeness note (05A §12)

Each flow above traces event → components → state → privileged ops → measurements → failures → recovery → visible outcome without unexplained jumps. Flows referencing device evidence carry their gating experiment IDs; none asserts device results (05A §13).
