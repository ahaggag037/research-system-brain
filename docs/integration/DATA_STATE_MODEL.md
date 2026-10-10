# DATA & STATE MODEL — FixHUA

> Canonical home for all important state (05A §6). Eight state classes; each object: owner, schema, lifecycle, persistence location, migration rule, corruption behavior, reset behavior, backup/restore. All schemas PROPOSED (no code exists); JSON schemas planned under `schemas/`.

## SO-1 Ephemeral runtime state

- **Owner:** FX-MODEL (estimates), FX-POLICY (current region), FX-SAFE (governor counters) — each owns its slice
- **Schema:** `StateEstimate`, `PredictionSet`, `RegionState { region, entered_at, dwell_expiry }`, `GovernorCounters { surface → { flips_1min, energy_today } }`
- **Lifecycle:** in-memory only; rebuilt from OBS within seconds of start
- **Persistence:** none (by design)
- **Migration:** n/a
- **Corruption:** n/a (rebuilt)
- **Reset:** process restart
- **Backup/restore:** none

## SO-2 Persistent configuration

- **Owner:** FX-STORE (writers: FX-UI for user prefs; FX-POLICY region tables versioned as shipped config)
- **Schema:** `Config { schema_version, sampling_profile, region_table_version, thresholds, dwell_ms, rate_limits, energy_budget, consent_tier, feature_flags }`
- **Lifecycle:** loaded at start; validated; on invalid → last-known-good snapshot
- **Persistence:** app-private DataStore (proto) + `config.snapshot` file
- **Migration:** forward migrations per schema_version; unknown newer version → refuse + stock defaults
- **Corruption:** checksum mismatch → restore snapshot; both bad → factory defaults (consent defaults DOWN)
- **Reset:** UI "reset settings"
- **Backup/restore:** user-initiated export excludes nothing sensitive here (no secrets in config)

## SO-3 Learned/adaptive state

- **Owner:** FX-MODEL (writes via FX-STORE)
- **Schema:** `LearnedModel { schema_version, markov_tables, context_features, calibration, sample_count, trained_window }`
- **Lifecycle:** created after minimum history; online bounded updates; decay on long absence
- **Persistence:** app-private file (binary or proto)
- **Migration:** version bump → migrate if defined, else reset to priors (never silent carry-over)
- **Corruption:** reset to priors + abstain (MODEL contract)
- **Reset:** UI "reset learning"
- **Backup/restore:** excluded from export (usage-derived; privacy boundary SAFETY_INVARIANTS §5)

## SO-4 Experiment state

- **Owner:** FX-SAFE (gating), records written by OBS/ACT
- **Schema:** `ExperimentState { experiment_id, phase: READY|RUN|DONE, canary_flags, results_ref }`
- **Lifecycle:** per E-xx definitions; results archived as files
- **Persistence:** app-private dir `experiments/`
- **Migration:** additive only
- **Corruption:** experiment marked INCONCLUSIVE, never auto-PASS
- **Reset:** per-experiment reset
- **Backup/restore:** user-initiated export allowed (no personal data — device metrics only)

## SO-5 Safety/rollback state

- **Owner:** FX-SAFE (writes), FX-ACT (stock snapshots)
- **Schema:** `StockSnapshot { surface, value, taken_at, device_fingerprint_hash* }`; `SafetyState { mode: NORMAL|DEGRADED|OBSERVATION_ONLY, frozen_surfaces[], reasons[] }` (*hash only, no raw identifiers — privacy boundary)
- **Lifecycle:** snapshot before first write per surface per device; safety mode transitions journaled
- **Persistence:** app-private + helper-side mirror for stock snapshots
- **Migration:** versioned; never drop stock snapshots on migration
- **Corruption:** if stock snapshot missing → surface is never written (fail-closed)
- **Reset:** only via full stock-restore + helper reinstall
- **Backup/restore:** excluded from export (device-tuned); rebuilt on device

## SO-6 Version/migration state

- **Owner:** FX-STORE
- **Schema:** `VersionState { app_version, schema_versions per object, last_migration, migration_log }`
- **Lifecycle:** checked at startup before any load
- **Persistence:** app-private
- **Migration:** itself append-only
- **Corruption:** treat all objects as unknown-version → safest path per object (defaults/priors/fail-closed)
- **Reset:** n/a
- **Backup/restore:** export included

## SO-7 Device capability state

- **Owner:** FX-OBS (probe writes), FX-SAFE (admission states)
- **Schema:** `CapabilityRegistry { device_class_hash*, entries: [{ surface: CS-*, detected, writable, observed_state: UNKNOWN|OBSERVED|USEFUL|SAFE|PRODUCTION|EXPERIMENTAL, evidence_refs: E-*, notes }] }`
- **Lifecycle:** probed at first run + after OS updates (build fingerprint change → reprobe)
- **Persistence:** app-private JSON
- **Migration:** additive; fingerprint change triggers reprobe, states reset to OBSERVED until re-evidenced
- **Corruption:** all surfaces → UNKNOWN (no actuation until probed)
- **Reset:** UI "re-probe device"
- **Backup/restore:** excluded (per-device by definition)

## SO-8 Audit/history state (journal)

- **Owner:** FX-SAFE (sole writer; append-only)
- **Schema:** `JournalEntry { seq, ts, kind: INTENT|ADMISSION|ACTUATION|READBACK|ROLLBACK|SAFETY|CONSENT|PROBE, payload, hash_chain }`
- **Lifecycle:** append forever with retention cap (ring, default 30 days); hash-chained for tamper-evidence
- **Persistence:** app-private append-only log, batched flush ≤1 Hz
- **Migration:** never migrated in place; new segment files per version
- **Corruption:** preserve tail; gap recorded; actuation blocked until journal writable (fail-closed)
- **Reset:** UI "clear history" (creates tombstone entry first)
- **Backup/restore:** user-initiated export allowed (on-device actions only; no personal content)

## Cross-object rules

- Single writer per object (enforced by construction: only the owner module imports the DAO).
- No object stores secrets except the helper token (FX-ACT key store entry, app-private, never exported, rotated on reinstall).
- All timestamps monotonic + wall-clock pair for reboot robustness.
