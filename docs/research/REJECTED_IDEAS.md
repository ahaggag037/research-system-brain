# REJECTED IDEAS — memory of dead ends

> 05 §10: why it looked promising; what invalidated it; permanent or conditional; what new evidence reopens it. Purpose: prevent future models from repeating dead ends without new evidence. Architecture-level rejects live in `../architecture/ALTERNATIVES.md`.

## RI-01 — RAM cleaning / wholesale background killing ("booster" pattern)

- **Why it looked promising:** immediate visible "free RAM" increase; market-familiar category.
- **What invalidated it:** directly attacks the mission's continuity goals (G3/G4/G5) — killed apps reload colder later; free RAM is not a user-visible outcome (R-045); AOSP manages memory via lmkd/freezer already with better information.
- **Permanent or conditional:** PERMANENT for this product.
- **Reopening evidence:** none conceivable within mission goals; a different product (one-shot cleanup tool) would be a different mission.

## RI-02 — Sustained maximum-clock pinning / performance governor lock

- **Why it looked promising:** trivially reduces scheduling latency variance; benchmark-visible.
- **What invalidated it:** unbounded thermal/battery cost (violates INV-9 spirit); thermal clamp then *worsens* variance (FF-4); fights the thermal stack's job. Bounded transient variant survives as CS-1 with caps/dwell — the sustained version is what is rejected.
- **Permanent or conditional:** PERMANENT for production; transient boosting retained separately.
- **Reopening evidence:** a device class with effectively unlimited thermal headroom — would still need energy-budget justification.

## RI-03 — Always-on Perfetto/system tracing as production telemetry

- **Why it looked promising:** richest signal quality (FrameTimeline, ftrace) — tempting as the OBS backbone.
- **What invalidated it:** overhead violates INV-8 (attack #2 in DESIGN_SPACE §5); tracing is a diagnostic instrument, not a production sensor.
- **Permanent or conditional:** PERMANENT as production telemetry; retained as user-initiated diagnostic sessions (IF-08).
- **Reopening evidence:** platform-level always-on tracing with <1% overhead becomes available and is verified by E-10-class measurement.

## RI-04 — "One magic sysctl set" for all states (universal swappiness etc.)

- **Why it looked promising:** simple, shippable, blog-popular.
- **What invalidated it:** 02 §6 — effects are state-dependent; swappiness semantics with zram (S-018) show the folklore version ("low = better") is wrong in both directions depending on state. Region-based policy (CS-5) replaces it.
- **Permanent or conditional:** PERMANENT as a static product; state-dependent region tables are the surviving form.
- **Reopening evidence:** E-06 showing state-independence on target class (would simplify CS-5 to a constant — recorded honestly as possible).

## RI-05 — Fighting EMUI background management head-on (silent counteraction)

- **Why it looked promising:** vendor cleaners are a top continuity killer (S-010); neutralizing them looks like the direct fix.
- **What invalidated it:** two controllers fighting = oscillation/instability (attack #3/#5); silent counteraction violates trust invariants (INV-5) and can trip vendor protections; unsustainable across EMUI versions (U-005).
- **Permanent or conditional:** PERMANENT as silent combat; coexistence + detection + user guidance (D-013, CS-10) is the surviving form.
- **Reopening evidence:** documented, stable, vendor-sanctioned APIs for policy cooperation.

## RI-06 — Kernel module / ROM integration for v1

- **Why it looked promising:** maximum authority (scheduler hooks, native prefetch — S-016).
- **What invalidated it:** hours-to-first-build (R-006), rollback/integrity cost, stock-installability. See `../architecture/ALTERNATIVES.md` ARCH-C/E.
- **Permanent or conditional:** CONDITIONAL (DEFERRED, research-only track S4).
- **Reopening evidence:** D-007 reopen condition — E-series proves userspace ceiling blocks SLO attainment.
