# DISCOVERY PROTOCOL
## Applies only before DESIGN_CONVERGED

Your job in this phase is to discover the strongest design.

Do not let final-document formatting become the main work.

---

## 1. Start from the underlying problem

Do not assume FixHUA is an optimizer, daemon, controller, diagnostic tool, AI system, policy manager, or any other familiar category.

Derive the product model from first principles.

Ask what actually causes the user-visible failures that matter and what kinds of mechanisms could materially change them.

---

## 2. Research freely

External research is encouraged.

Prefer high-quality primary or authoritative sources when consequential claims depend on them.

Community reports can be useful as leads or field evidence, but label them appropriately.

Record consequential sources in the Source Index.

Do not treat the internet’s consensus language as a substitute for mechanism analysis.

---

## 3. Generate before judging

For promising concepts use:

`GENERATE → EXTEND → COMBINE → MUTATE → MODEL → THEN JUDGE`

Avoid killing ideas before their structure is understood.

Before discarding an unusual idea, ask whether:
- only one part is valuable,
- timing rather than magnitude is the key,
- a short-lived variant changes the risk,
- another mechanism neutralizes the downside,
- a predictive variant beats a reactive one,
- two weak ideas combine into a strong one,
- or a safer proxy preserves most of the benefit.

---

## 4. Science-selection rule

When a problem resists ordinary Android thinking, ask:

> Which scientific or engineering discipline has better tools for this class of problem?

Do not begin with a predetermined list.

Search outward as needed.

When transferring a concept, translate it into:

`source concept → device mechanism → observable signal → controllable variable → expected effect → uncertainty → experiment → recovery`

If the translation is weak, the analogy is decorative and should not drive the design.

---

## 5. Risk transmutation

For every high-impact idea that is commonly considered risky:

1. identify why;
2. decompose the hazard;
3. model the hazardous dimension when useful;
4. search for a bounded variant;
5. identify what must be observed;
6. define the smallest falsification experiment;
7. define abort/recovery conditions;
8. decide whether it belongs in:
   - production candidate,
   - experimental candidate,
   - research-only,
   - or rejected.

Do not treat risk reduction as guaranteed benefit preservation. Measure both.

---

## 6. Prefer state-aware mechanisms when state matters

Avoid universal “magic values” when the effect is state-dependent.

Ask:
- when does the intervention help?
- when does it hurt?
- when should it be refused?
- when should it be reversed?

Use operating regions when appropriate.

---

## 7. Information-gain testing

The goal is not the fewest tests at any cost.

The goal is the **smallest set of tests that removes the most consequential uncertainty**.

Prefer experiments that can falsify multiple assumptions or architecture branches at once.

High-risk production candidates require more evidence than low-risk observation-only ideas.

---

## 8. Hours-to-first-usable-build objective

The research is not timeboxed.

The implementation strategy should still search for a useful first build that can be produced in hours where feasible.

Prefer early components that:
- create immediate user or diagnostic value,
- reveal device truth,
- falsify major assumptions,
- and form a foundation for later work.

Separate:
- first useful build,
- first daily-usable build,
- long-term architecture.

---

## 9. Architecture challenger rule

Do not fall in love with the first coherent architecture.

Maintain serious challengers until one clearly dominates.

When an architecture changes materially:
- update the Decision Log,
- mark superseded decisions,
- update the Checkpoint,
- re-check affected requirements.

---

## 10. Adversarial self-attack

Before convergence, try to break the leading design.

Test whether:
- it optimizes a symptom instead of a cause;
- telemetry overhead destroys the gain;
- controllers fight;
- feedback oscillates;
- vendor policy overrides it;
- short-term gains create sustained regressions;
- learning fits noise;
- root infrastructure creates more instability than value;
- or the project is solving the wrong abstraction.

Redesign if necessary.

---

## 11. Convergence criteria

Declare `DESIGN_CONVERGED` only when most are true:

- the core problem model is stable;
- major alternatives were seriously considered;
- the selected architecture survived adversarial attack;
- recent research mostly refines rather than overturns major decisions;
- critical unknowns are explicit;
- implementation-blocking unknowns have concrete discovery tests;
- the first useful build is clear;
- important risky ideas have a disposition;
- and the architecture can be explained coherently end-to-end.

Do not wait for perfect knowledge.

Do not converge merely because the design sounds polished.
