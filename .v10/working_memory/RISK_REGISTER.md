# RISK REGISTER

## Risk template

RISK_ID:
STATUS:
MECHANISM:
IMPACT:
LIKELIHOOD_OR_UNCERTAINTY:
TRIGGER:
DETECTION:
MITIGATION:
ABORT_CONDITION:
RECOVERY:
AFFECTED_COMPONENTS:
RELATED_EXPERIMENTS:

---

## RSK-001 — Session/sandbox state loss before GitHub persistence
STATUS: MITIGATED
MECHANISM: Sandbox storage outside the repo is transient; a prior session lost all work when no push path existed.
IMPACT: Total mission state loss; restart from package.
LIKELIHOOD_OR_UNCERTAINTY: Was HIGH; now LOW after D-003 (API push confirmed) and checkpoint-per-event discipline (R-051).
TRIGGER: Any context reset, tool loss, or sandbox release.
DETECTION: Repo branch/commit mismatch vs CHECKPOINT.md.
MITIGATION: CP-001 pushed before deep Discovery (R-064); push at every 00B §5 checkpoint event; local working copy is only a mirror.
ABORT_CONDITION: Repeated API write failure → blocker protocol R-024, continue local canonical files, document recovery path.
RECOVERY: Re-clone repo; follow 03 §5 recovery procedure via AGENTS.md.
AFFECTED_COMPONENTS: Entire mission state.
RELATED_EXPERIMENTS: —

## RSK-002 — Blind-condition contamination
STATUS: OPEN (standing)
MECHANISM: Accidental exposure to prior FixHUA material via search results, repo suggestions, or parallel outputs.
IMPACT: Independence of design compromised; evidence base polluted.
LIKELIHOOD_OR_UNCERTAINTY: LOW with query discipline; non-zero.
TRIGGER: Search results referencing user's repositories or prior design discussions.
DETECTION: Source review before consumption (SOURCE_INDEX hygiene, R-030).
MITIGATION: Query scoping to public technical domains; never open user-repo links; contamination protocol R-035 if exposure occurs.
ABORT_CONDITION: N/A (mission continues; exposure is disclosed and excluded from evidence).
RECOVERY: Record event, identify exposed ideas, exclude from evidence, continue independently (00B §7).
AFFECTED_COMPONENTS: Evidence base, DECISION_LOG.
RELATED_EXPERIMENTS: —

## RSK-003 — Product-level risks
STATUS: SUPERSEDED
MECHANISM: Placeholder from STATE A; product risks now enumerated as RSK-101..RSK-110 (STATE B).
IMPACT: —
LIKELIHOOD_OR_UNCERTAINTY: —
TRIGGER: —
DETECTION: —
MITIGATION: —
ABORT_CONDITION: —
RECOVERY: —
AFFECTED_COMPONENTS: —
RELATED_EXPERIMENTS: —

## RSK-101 — Controller interference with vendor performance manager
STATUS: OPEN (design mitigation embedded)
MECHANISM: Vendor perfmgr/power HAL and FixHUA both write overlapping surfaces (boosts, cgroup values) → additive overreaction or oscillation.
IMPACT: Instability, regressions, wasted energy.
LIKELIHOOD_OR_UNCERTAINTY: HIGH on EMUI-class devices without mitigation.
TRIGGER: Concurrent writes to same sysfs/cgroup node within dwell window.
DETECTION: Ownership map; unexpected external change detection in ACT readback verification.
MITIGATION: Single-writer rule per control; vendor-boost detection + deferral; gap-filling only (D-013); dwell/rate limits.
ABORT_CONDITION: Oscillation detected (state flip >3×/min sustained) → freeze actuation, restore stock, alert.
RECOVERY: Rollback journal restores last stock values.
AFFECTED_COMPONENTS: FX-POLICY, FX-ACT.
RELATED_EXPERIMENTS: E-02, E-06.

## RSK-102 — lmkd restart protection gap
STATUS: OPEN — gated EXPERIMENTAL (E-11)
MECHANISM: Restarting lmkd to apply ro.lmk.* changes leaves a short window with weakened kill policy.
IMPACT: Potential OOM at kernel level or wrong kill during gap.
LIKELIHOOD_OR_UNCERTAINTY: MEDIUM; window measurable on bench.
TRIGGER: lmkd restart action.
DETECTION: Supervised canary; kill logs.
MITIGATION: Canary-only, never default; pre/post health checks; immediate restore script ready.
ABORT_CONDITION: Any system_server instability or unexplained kill → instant stock restore.
RECOVERY: Stock property restore + lmkd restart + reboot option.
AFFECTED_COMPONENTS: FX-ACT (lmkd adapter), FX-SAFE.
RELATED_EXPERIMENTS: E-11.

## RSK-103 — oom_score_adj overwrite race with AMS
STATUS: OPEN
MECHANISM: AMS/OomAdjuster recomputes adj on lifecycle events; external nudges transient.
IMPACT: Policy silently ineffective (harmless) or mistimed (kill-order perturbation).
LIKELIHOOD_OR_UNCERTAINTY: HIGH that overwrites happen; timing unknown (E-03).
TRIGGER: Any lifecycle recompute.
DETECTION: E-03 overwrite timing log.
MITIGATION: Timed application at events; tolerate overwrite; drop if noise (E-03 gate).
ABORT_CONDITION: Unexpected kill pattern attributable to nudges.
RECOVERY: Stop nudging; stock adj recomputed by AMS automatically.
AFFECTED_COMPONENTS: FX-ACT (adj adapter).
RELATED_EXPERIMENTS: E-03.

## RSK-104 — Telemetry overhead exceeds budget
STATUS: OPEN (bounded by design)
MECHANISM: Polling/tracing CPU+wakelocks cost battery; Perfetto always-on too heavy (S-021).
IMPACT: Net user harm — violates real-value invariant (01 §8).
LIKELIHOOD_OR_UNCERTAINTY: LOW with trigger-based design; verified by E-10.
TRIGGER: Sampling rate creep; Perfetto left on.
DETECTION: Self-profiling counters; E-10 measurement.
MITIGATION: Event-driven PSI; ≤1 Hz pollers; Perfetto diagnostic-only; adaptive down-sampling under load; budget gate <1% CPU.
ABORT_CONDITION: E-10 breach → forced rate reduction.
RECOVERY: Config reset to minimal profile.
AFFECTED_COMPONENTS: FX-OBS.
RELATED_EXPERIMENTS: E-10.

## RSK-105 — Predictor arms harmful action
STATUS: OPEN (bounded by design)
MECHANISM: Wrong next-app prediction triggers prefetch/protect for wrong app (memory waste, wrong kill-protection).
IMPACT: Memory pressure increase; wasted I/O.
LIKELIHOOD_OR_UNCERTAINTY: MEDIUM until E-09 quantifies accuracy.
TRIGGER: Confidence below threshold or distribution shift.
DETECTION: Online accuracy tracking; regret accounting.
MITIGATION: Only low-regret actions armable (D-012); confidence gating + abstention; bounded prefetch size (S-016); idle-only arming.
ABORT_CONDITION: Measured regret above bound → disable predictive arming, keep heuristics.
RECOVERY: Disable CF-6 via config; models resettable.
AFFECTED_COMPONENTS: FX-MODEL, FX-POLICY.
RELATED_EXPERIMENTS: E-09.

## RSK-106 — Battery regression from interventions
STATUS: OPEN
MECHANISM: Boosts/wakefulness raise energy draw.
IMPACT: Battery life loss — user-visible harm.
LIKELIHOOD_OR_UNCERTAINTY: MEDIUM without guards.
TRIGGER: Sustained boost overuse; background churn.
DETECTION: BatteryStats/HAL energy counters vs baseline; per-action energy accounting.
MITIGATION: Energy budget in policy objective; boost duration caps; auto-degrade on budget breach.
ABORT_CONDITION: Budget breach 2 consecutive days → degrade to observation-only.
RECOVERY: Stock values; observation mode.
AFFECTED_COMPONENTS: FX-POLICY, FX-ACT.
RELATED_EXPERIMENTS: E-02, E-10.

## RSK-107 — Privileged helper compromise / misuse
STATUS: OPEN (mitigations structural)
MECHANISM: Root daemon with write access to system surfaces is an attack surface.
IMPACT: Privilege escalation vector if hijacked.
LIKELIHOOD_OR_UNCERTAINTY: LOW with schema-only protocol; unbounded if shell passthrough existed (it does not — hard ban, R-040).
TRIGGER: Malformed request; local socket scan; token leak.
DETECTION: Audit journal anomalies; auth failures.
MITIGATION: UNIX socket with per-install token + peer UID check; fixed command schema (no arbitrary commands); no network; SELinux respecting; minimal code size; open for audit (public repo).
ABORT_CONDITION: Auth failure burst → helper shuts down actuation.
RECOVERY: Reinstall helper; rotate token; stock restore.
AFFECTED_COMPONENTS: FX-ACT, FX-SAFE.
RELATED_EXPERIMENTS: — (security review at BUILD_READY).

## RSK-108 — Thermal protection tampering (FORBIDDEN PATH)
STATUS: CLOSED-BY-INVARIANT
MECHANISM: Writing thermal trip points / disabling thermal-engine would remove hardware protection.
IMPACT: Hardware damage risk.
LIKELIHOOD_OR_UNCERTAINTY: N/A — prohibited.
TRIGGER: Any code path attempting thermal config writes.
DETECTION: Static rule: no thermal-write adapter exists; review gate.
MITIGATION: Hard invariant (R-040): FixHUA never writes thermal protection configuration; CF-5 is advisory load-shaping only.
ABORT_CONDITION: N/A.
RECOVERY: N/A.
AFFECTED_COMPONENTS: none (by construction).
RELATED_EXPERIMENTS: E-08 (read-only thermal observation).

## RSK-109 — EMUI/version surface variance breaks assumptions
STATUS: OPEN
MECHANISM: Vendor customization changes file paths, policies, APIs across versions.
IMPACT: Controls missing/misbehaving on some devices.
LIKELIHOOD_OR_UNCERTAINTY: HIGH (U-005).
TRIGGER: New EMUI/Android version.
DETECTION: Boot-time capability probe matrix; readback verification after every write.
MITIGATION: Capability registry per device; graceful degrade; never assume unobserved control (R-043).
ABORT_CONDITION: Probe mismatch on critical control → that control disabled on device.
RECOVERY: Disable affected adapters; observation continues.
AFFECTED_COMPONENTS: FX-ACT (all adapters), FX-OBS.
RELATED_EXPERIMENTS: E-12.

## RSK-110 — User trust/consent failure
STATUS: OPEN
MECHANISM: Opaque privileged behavior erodes trust even if technically safe.
IMPACT: Product rejection; uninstall.
LIKELIHOOD_OR_UNCERTAINTY: MEDIUM without transparency.
TRIGGER: Silent policy change; unexpected behavior.
DETECTION: User feedback; audit log visibility.
MITIGATION: Consent gates per capability tier; visible action journal; one-tap kill switch; honest truth states in UI.
ABORT_CONDITION: N/A.
RECOVERY: Disable all actuation from UI instantly.
AFFECTED_COMPONENTS: FX-UI, FX-SAFE.
RELATED_EXPERIMENTS: —
