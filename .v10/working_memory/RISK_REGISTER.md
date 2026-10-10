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
STATUS: DEFERRED to STATE B/C
MECHANISM: To be enumerated when architecture candidates exist (01 §2 protocol: hazard mechanism → boundedness → redesign → research-only if unbounded).
IMPACT: TBD.
LIKELIHOOD_OR_UNCERTAINTY: TBD.
TRIGGER: TBD.
DETECTION: TBD.
MITIGATION: TBD.
ABORT_CONDITION: TBD.
RECOVERY: TBD.
AFFECTED_COMPONENTS: TBD.
RELATED_EXPERIMENTS: TBD.
