# DECISION LOG

## Decision template

DECISION_ID:
STATUS: ACTIVE | SUPERSEDED | REJECTED | PROVISIONAL
DATE/SEQUENCE:
CONTEXT:
OPTIONS:
DECISION:
EVIDENCE:
ASSUMPTIONS:
CONSEQUENCES:
RISKS:
REOPEN_IF:
SUPERSEDES:
SUPERSEDED_BY:

---

## D-001 — Repository layout
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 1 (STATE A)
CONTEXT: Empty authorized repo; 00B §3 requires eventual canonical surfaces; need a layout that keeps mission contract, working memory, machine state, and future product docs cleanly separated.
OPTIONS: (a) flat root dump; (b) `mission/` + `.v10/` + future `docs/` separation; (c) docs-only repo with memory elsewhere.
DECISION: Layout (b): `mission/` = immutable canonical V10.3 contract snapshot (excluding `_human/`); `.v10/working_memory/` = operational scratch (03 §1); `.v10/state/` = machine-readable state; `docs/`, `audit/`, `dist/` to be added in STATE C/D; root `AGENTS.md` + `README.md` as entry surfaces.
EVIDENCE: 00B §3–§4 layout guidance; 03 §1.
ASSUMPTIONS: Future states will not need layout migration; if they do, recheck per 04 §5.
CONSEQUENCES: Clean cold-start navigation; mission contract travels with repo.
RISKS: Low; dot-directory `.v10` is visible on GitHub, acceptable.
REOPEN_IF: A packaging contract (STATE C) mandates a different canonical layout.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-002 — Exclusion of `_human/` from repository snapshot
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 2 (STATE A)
CONTEXT: Package contains `_human/ARCHITECTURE_REVIEW.md` marked `canonical: false` in manifest; 00 §1 forbids the executing model from reading `_human/`; 00B §8 cold-start test means a future model will read this repo.
OPTIONS: (a) include `_human/` unread; (b) exclude it entirely.
DECISION: Exclude `_human/` from the repo. Never read it (R-010). Excluding it also protects the blind condition for any future cold-start model continuing from GitHub.
EVIDENCE: PACKAGE_MANIFEST.json (`canonical: false`); 00 §1; 00B §8.
ASSUMPTIONS: User retains `_human/` in the original ZIP outside the repo.
CONSEQUENCES: Repo holds only model-canonical mission files.
RISKS: None identified; the file is human-facing by design.
REOPEN_IF: User explicitly instructs otherwise.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-003 — GitHub API is the persistence path (not sandbox git push)
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 3 (STATE A)
CONTEXT: Sandbox has git but no GitHub credentials; authenticated GitHub API access (login `ahaggag037`) IS available this session via the GitHub integration.
OPTIONS: (a) attempt git push and fail; (b) commit via API file operations; (c) local-only persistence with blocker note.
DECISION: Commit via authenticated GitHub API directly to `main`. Local working copy at sandbox path is a staging mirror, not canonical.
EVIDENCE: Authenticated identity verified; repo branch list initially empty; API write confirmed at CP-001 push.
ASSUMPTIONS: API access persists for the session. If it fails mid-mission → blocker protocol R-024 (record, smallest recovery = retry/alternate batching; continue local work).
CONSEQUENCES: Commit granularity = meaningful checkpoints (00B §5), not per-file noise.
RISKS: API rate limits on large batches → mitigate with batched multi-file commits.
REOPEN_IF: API write fails repeatedly.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-004 — AGENTS.md as the cold-start continuation surface
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 4 (STATE A)
CONTEXT: 00B §3 requires root AGENTS.md; 00B §8 cold-start test needs an unambiguous entry that routes a future model to checkpoint → ledger → decisions → current state file (03 §5).
OPTIONS: (a) README-only; (b) AGENTS.md as machine-continuation entry + README as human entry; (c) single combined doc.
DECISION: (b). AGENTS.md = operational continuation contract; README.md = human-facing project entry.
EVIDENCE: 00B §3, §8; 03 §5.
ASSUMPTIONS: Future models check AGENTS.md by convention.
CONSEQUENCES: AGENTS.md must be updated at every state transition (add to checkpoint checklist).
RISKS: Staleness → mitigated by checkpoint discipline (R-051).
REOPEN_IF: Final audit finds cold-start ambiguity.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-005 — Full mission contract snapshot staged to GitHub before deep Discovery
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 5 (STATE A, user-directed)
CONTEXT: User directive (mission restart turn): "Before beginning deep Discovery, make the mission recoverable from GitHub: persist the canonical mission bootstrap/control files and initialized working-memory state." 00 §4 progressive disclosure exists to protect discovery from documentation pressure — a readability concern, not a secrecy one.
OPTIONS: (a) push only STATE A files, defer 02/05–09 until their states; (b) stage all 16 model files now per user directive.
DECISION: (b). All `mission/model/` files pushed at CP-001. Progressive *activation* is preserved: 02 governs STATE B (begins now); 05/05A/05B/06 govern packaging and are inert until DESIGN_CONVERGED; 07/08 inert until STATE D. R-017 (no final-doc polish while architecture moves) unaffected.
EVIDENCE: User directive text; R-064.
ASSUMPTIONS: Visibility of later contracts does not bias discovery toward premature packaging; discovery protocol 02 §1–§2 keeps focus on problem-first work.
CONSEQUENCES: GitHub alone is sufficient to recover the full mission after any reset (the user's stated goal). Ledger notes record the staging-vs-activation distinction.
RISKS: Minor anchoring risk from packaging contracts; mitigated by explicit inert-until-state rule.
REOPEN_IF: User revokes directive or control plane reinterpretation required.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-006 — Product category: adaptive continuity control plane
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 6 (STATE B)
CONTEXT: R-005 forbids assuming category. Problem decomposition (DESIGN_SPACE §1) shows four failure families rooted in reactive, per-resource, uncoordinated stock control at transitions/contention spikes.
OPTIONS: booster/cleaner app; static tuning pack; diagnostic-only tool; kernel mod; adaptive control plane.
DECISION: FixHUA = adaptive continuity control plane: OBS→MODEL→POLICY→ACT under SAFE, with consent UI. Diagnosis is the first deliverable (FB-1), not the whole product.
EVIDENCE: DESIGN_SPACE §1–§2; S-002..S-021 mechanism base; S-011 (platform itself validates hint-adaptive model).
ASSUMPTIONS: Root available on target (mission-granted when justified); device observation gates all device-specifics.
CONSEQUENCES: Component model FX-OBS/MODEL/POLICY/ACT/SAFE/UI/STORE; success = user-visible SLOs only.
RISKS: RSK-101..110 tracked.
REOPEN_IF: E-01/E-02 falsify core levers AND no substitute levers exist.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-007 — Architecture selection: ARCH-FX-CP-01 over A/C/D/E
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 7 (STATE B)
CONTEXT: 02 §9 challenger rule.
OPTIONS: ARCH-A static pack (rejected: mission anti-goal); ARCH-C kernel module (rejected v1: rollback/integrity/hours); ARCH-D app-only (kept as degraded mode); ARCH-E ROM mod (rejected: not installable on stock); ARCH-B userspace control plane.
DECISION: ARCH-B refined → ARCH-FX-CP-01 "CONTINUUM": OBS (telemetry) / MODEL (state+prediction) / POLICY (decision engine) / ACT (whitelist adapters) / SAFE (admission+governor) / UI (consent+diagnosis) / STORE (state). Challengers documented in DESIGN_SPACE §4.
EVIDENCE: DESIGN_SPACE §4–§5 (adversarial attack resolutions).
ASSUMPTIONS: Userspace authority sufficient — to be confirmed by E-01..E-04 (if ceiling proven, ARCH-C revisit allowed with evidence).
CONSEQUENCES: Hours-to-first-build feasible (FB-1); all privileged surfaces enumerated and gated.
RISKS: RSK-101, RSK-109.
REOPEN_IF: E-series evidence shows userspace ceiling blocks SLO attainment.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-008 — Observation-first rollout
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 8 (STATE B)
CONTEXT: 02 §8 (first build reveals device truth, falsifies assumptions, immediate value); 01 §6 (root ≠ evidence).
DECISION: FB-1 ships OBS+UI diagnosis only, zero actuation. Actuation enters at FB-2 in shadow→canary behind consent.
EVIDENCE: 02 §8 criteria; DESIGN_SPACE CF-1.
CONSEQUENCES: First build in hours; E-01/E-03/E-05/E-10/E-12 run on FB-1.
RISKS: None material.
REOPEN_IF: —
SUPERSEDES: —
SUPERSEDED_BY: —

## D-009 — Privilege model: minimal schema-bound root helper
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 9 (STATE B)
CONTEXT: 01 §3 bans unrestricted arbitrary-root endpoints; RSK-107.
DECISION: Privileged native helper (root) exposes ONLY a fixed, versioned, schema-validated command set over a local UNIX socket with per-install token + peer-UID auth. No shell passthrough, no network, no eval. App is unprivileged; absent root → degraded mode.
EVIDENCE: 01 §3 admission rules; DESIGN_SPACE attack #8.
CONSEQUENCES: Every privileged action enumerated in component contracts; audit journal mandatory.
RISKS: RSK-107 residual → security review at BUILD_READY.
REOPEN_IF: A required control cannot fit schema model (then extend schema explicitly, never genericize).
SUPERSEDES: —
SUPERSEDED_BY: —

## D-010 — Control admission pipeline
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 10 (STATE B)
CONTEXT: 01 §3/§6: privileged controls need observed→useful→safe evidence; 02 §5 dispositions.
DECISION: Every control surface carries a state machine: UNKNOWN → OBSERVED → USEFUL(evidence) → SAFE(bounds) → PRODUCTION; plus EXPERIMENTAL gate for high-risk (e.g., lmkd retune). SAFE enforces: no production action from controls below SAFE state on that device.
EVIDENCE: R-040/R-043; E-series mapping.
CONSEQUENCES: Device capability registry is a core STORE object; config per device.
RISKS: Slower rollout on new devices (accepted).
REOPEN_IF: —
SUPERSEDES: —
SUPERSEDED_BY: —

## D-011 — Visual twin format: Mermaid (+SVG renders in audit)
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 11 (STATE B)
CONTEXT: 06 §2: editable repo-friendly format when Figma unavailable; Figma unavailable here.
DECISION: Mermaid sources in docs/visuals/*.mmd + VISUAL_TRACEABILITY.json mapping node IDs→components/docs/tests; renders generated at packaging/audit.
EVIDENCE: 06 §2 list.
CONSEQUENCES: All diagrams text-diffable; traceability enforced in audit.
RISKS: Complex flows may need multiple diagrams (accepted).
REOPEN_IF: —
SUPERSEDES: —
SUPERSEDED_BY: —

## D-012 — Prediction arms only low-regret actions
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 12 (STATE B)
CONTEXT: Attack #7 (learning fits noise); RSK-105.
DECISION: Predictor output can arm ONLY: bounded page-cache warm, kill-protection nudge, boost pre-arm. Never kills, never freezes, never irreversible. Confidence gate + abstention + online regret accounting (E-09).
EVIDENCE: S-015/S-016 (method + misprediction costs).
CONSEQUENCES: Misprediction worst case = small wasted memory/I-O, self-correcting.
RISKS: Value ceiling if accuracy low → heuristic fallback defined.
REOPEN_IF: E-09 results.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-013 — Vendor-policy coexistence, not combat
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 13 (STATE B)
CONTEXT: EMUI layered kill policies (S-010); fighting vendor policy causes oscillation/instability.
DECISION: Detect vendor policy state (E-12); align policy around it; advise user through documented settings when beneficial; never silently counteract. Single-writer rule per control surface.
EVIDENCE: S-010; DESIGN_SPACE attack #3/#5.
CONSEQUENCES: FX-OBS includes vendor-policy detection; UI includes guidance flow.
RISKS: Coverage gaps → manual checklist fallback.
REOPEN_IF: E-12.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-014 — Target bounding: Android-based Huawei-class devices
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 14 (STATE B)
CONTEXT: "Huawei-class Android phone" needs scoping; HarmonyOS NEXT/5 has no AOSP core and cannot run Android apps (S-020).
DECISION: Target = Android(AOSP)-based Huawei devices (EMUI; dual-framework HarmonyOS ≤4), Arm big.LITTLE SoCs (Kirin class), kernel ≥4.19+ (PSI/cgroup v2 features verified per device via capability probe). HarmonyOS NEXT is out of scope (different OS). Core design is AOSP-general with a device-adaptation layer.
EVIDENCE: S-020; kernel feature requirements from S-004/S-007/S-013.
CONSEQUENCES: Capability probe matrix is mandatory at first run (RSK-109).
RISKS: EMUI variance (U-005).
REOPEN_IF: User narrows to a specific device model.
SUPERSEDES: —
SUPERSEDED_BY: —

## D-015 — No-device truth policy for this package
STATUS: ACTIVE
DATE/SEQUENCE: 2026-10-10 / seq 15 (STATE B)
CONTEXT: No physical device in environment (00A §4 blocker category; 05A §13).
DECISION: All device-dependent claims remain HYPOTHESIS/UNKNOWN with ready-to-run experiment definitions (E-01..E-12). Package reports implementation readiness as FULLY_SPECIFIED with device-validation pending — never as tested-on-target.
EVIDENCE: 01 §4/§9; 05A §13.
CONSEQUENCES: Audit must verify no truth-state inflation.
RISKS: None (honesty preserved).
REOPEN_IF: Device evidence arrives.
SUPERSEDES: —
SUPERSEDED_BY: —
