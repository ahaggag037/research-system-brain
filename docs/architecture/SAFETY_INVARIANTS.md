# SAFETY INVARIANTS — FixHUA

> Canonical home for safety invariants and the privileged-change admission pipeline. Violating an invariant is a release-blocking defect. Sources: mission `01` §3/§6/§8, risks RSK-101..110, decisions D-009/D-010.

## 1. Hard invariants (never violated by any build, config, or experiment)

- **INV-1 Thermal protection.** FixHUA never writes thermal trip points, thermal-engine config, or any hardware protection parameter. Thermal work is read-only observation + advisory load shaping (CS-8). (RSK-108: CLOSED-BY-INVARIANT — no thermal-write adapter exists by construction.)
- **INV-2 No integrity/account bypasses.** No account, banking, DRM, verified-boot, anti-tamper, or app-integrity bypasses; no interference with other apps' security posture.
- **INV-3 No arbitrary-root endpoint.** `fxhelperd` exposes only the fixed versioned schema command set; no shell passthrough, no eval, no network listeners. Any new privileged action = explicit schema extension + review (D-009).
- **INV-4 Observation is always safe.** Disabling all actuation (kill switch) must leave the device at stock behavior; FixHUA must never require itself to keep the system stable.
- **INV-5 No silent privileged change.** Every actuation is journaled (FX-SAFE audit) and visible in FX-UI.
- **INV-6 Reversibility.** Every production action has a defined rollback executed on abort; stock values are recorded before first write per surface per device.
- **INV-7 Consent tiers.** Diagnosis (tier 1) precedes actuation (tier 2) precedes experimental (tier 3); no tier is enabled silently.

## 2. Boundedness invariants (engineered limits)

- **INV-8 Telemetry budget:** production telemetry < 1% average CPU, ~0 wakelocks (E-10 gate). Perfetto = diagnostic sessions only.
- **INV-9 Energy budget:** cumulative intervention energy per day bounded; breach → auto-degrade to observation (RSK-106).
- **INV-10 Oscillation guard:** any controlled surface flipping >3×/min sustained → freeze actuation, restore stock, alert (RSK-101).
- **INV-11 Single writer:** if a vendor/other controller is detected writing a surface, FixHUA defers on that surface (D-013).
- **INV-12 Prediction regret bound:** armed predictive actions are only from the low-regret set; measured regret above bound disables arming (D-012, E-09).

## 3. Privileged-change admission pipeline (01 §3/§6; D-010)

Every control surface on every device class passes:

```
UNKNOWN → OBSERVED → USEFUL → SAFE → PRODUCTION
                     │
                     └── EXPERIMENTAL gate (high-risk, e.g., CS-9): canary-only, supervised, never default
```

| Stage | Meaning | Evidence required |
|---|---|---|
| UNKNOWN | surface assumed to exist | — (never actuated) |
| OBSERVED | detected + read on this device class | capability probe entry (E-12-class) |
| USEFUL | measurable user-visible effect | experiment pass (E-02/E-04-class) |
| SAFE | boundedness demonstrated: abort, rollback, no instability window | risk row closed + failure-injection test |
| PRODUCTION | may run under SAFE governor in release builds | all above + consent tier 2 |

FX-SAFE enforces at runtime: **no production action from a control below SAFE on the current device**; EXPERIMENTAL controls require tier-3 consent + canary flags.

## 4. Forbidden paths (design-excluded)

| Path | Status | Reason |
|---|---|---|
| Thermal config writes | FORBIDDEN | INV-1 |
| Arbitrary root shell/RPC | FORBIDDEN | INV-3 |
| Silent background policy changes | FORBIDDEN | INV-5/INV-7 |
| Wholesale process killing ("RAM cleaning") | REJECTED | harms continuity goal; see `../research/REJECTED_IDEAS.md` RI-01 |
| Sustained max-clock pinning | REJECTED for production | thermal/battery cost unbounded; RI-02 |
| Kernel module/patch v1 | DEFERRED | rollback/integrity cost vs userspace ceiling unproven (D-007 reopen condition) |

## 5. Data/privacy boundaries

- No secrets, tokens, keys, personal or device-identifying data in the repository (public repo — R-032).
- Telemetry/learning data stays on-device; no network exfiltration path exists in the design (fxhelperd has no network; app requests no network permission for telemetry).
- The per-install helper token is generated on device, stored in app-private storage, never leaves the device.
