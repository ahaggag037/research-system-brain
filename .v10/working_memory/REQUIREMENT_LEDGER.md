# REQUIREMENT LEDGER

Compiled at STATE A initialization (checkpoint CP-001) from currently loaded canonical files:
`mission/model/00_CONTROL_PLANE.md` (00), `00A_SINGLE_SESSION_CONTRACT.md` (00A),
`00B_CLEAN_REPOSITORY_BOUNDARY.md` (00B), `01_ENGINEERING_CONSTITUTION.md` (01),
`03_WORKING_MEMORY_PROTOCOL.md` (03), `04_COMPLIANCE_SHELL.md` (04).

Later files (02, 05, 05A, 05B, 06, 07, 08) will extend this ledger when their state loads them (00 §4 progressive disclosure; 04 §1).

| ID | Source | Priority | Requirement | Acceptance evidence | Artifact/path | Status | Blocker/notes |
|---|---|---|---|---|---|---|---|
| R-001 | 00 §0 | MUST | Design FixHUA independently from first principles for a Huawei-class Android phone | Architecture derivations traceable to independent research/reasoning in DECISION_LOG and docs | docs/ (STATE C) | OPEN | |
| R-002 | 00 §0 | MUST | Execute exclusively inside ahaggag037/research-system-brain on main | Canonical state present in repo | entire repo | IN_PROGRESS | |
| R-003 | 00 §0 | MUST | Design targets: responsiveness; smooth interaction; less stutter/jank/long pauses; multitasking continuity; fewer avoidable reloads/exits; better resume quality; sustained stability; predictability under changing workloads; intelligent management (not static tweak collection) | Architecture and metrics map each outcome to mechanisms | docs/ (STATE C) | OPEN | |
| R-004 | 00 §0 | MUST | Root access assumed available when justified; root is a capability, not the product definition | Privileged mechanisms individually justified; non-root baseline exists | docs/ + DECISION_LOG | OPEN | |
| R-005 | 00 §0 | MUST | Do not assume the correct product category in advance | DECISION_LOG records category exploration before selection | .v10/working_memory/DECISION_LOG.md | OPEN | |
| R-006 | 00 §0 | MUST | Credible path to useful installable/testable first build in hours of focused engineering where feasible; long-term design may be richer | Build/runbook defines fast first build | docs/build* (STATE C) | OPEN | |
| R-007 | 00 §0 | SHOULD | No fixed research deadline; use diminishing marginal value of new information as stopping rule | Discovery notes record why research stopped | .v10/working_memory/ACTIVE_NOTES.md | OPEN | |
| R-008 | 00 §1 | MUST | Blindness: do not inspect/search/consume predecessor FixHUA architecture, prompts, research, component names, diagrams, design discussions | SOURCE_INDEX contains only independent public sources; contamination register empty or disclosed | .v10/working_memory/SOURCE_INDEX.md | IN_PROGRESS | Ongoing duty |
| R-009 | 00 §1 | MAY | Independent public research allowed (Android, Linux, Huawei, SoCs, OS, perf engineering, academia, OSS, math) | Sources logged | .v10/working_memory/SOURCE_INDEX.md | OPEN | |
| R-010 | 00 §1 | MUST | Do not read `_human/` | `_human/` never opened; excluded from repo snapshot | DECISION_LOG D-002 | SATISFIED | Standing duty for remainder of mission |
| R-011 | 00 §2 | MUST | Permanent principle: think freely, engineer ruthlessly, build strategically, preserve truth; optimize the underlying problem, not prompt satisfaction | Design artifacts show rejected weak ideas + honest unknowns | docs/, DECISION_LOG | OPEN | |
| R-012 | 00 §3 | MUST | Run entire state machine in one continuous session; transitions automatic, no user authorization | Uninterrupted A→B→C→D execution | session record | IN_PROGRESS | |
| R-013 | 00 §3 STATE A | MUST | Read 00A, 00B, 01, 03, 04 at initialization | All five read before this ledger | this ledger header | SATISFIED | |
| R-014 | 00 §3 STATE A; 03 §1 | MUST | Create the 8 canonical working-memory files from templates | Files exist and are maintained | .v10/working_memory/ | SATISFIED | CP-001 |
| R-015 | 00 §3 STATE A | MUST | Build the first Requirement Ledger | This file | .v10/working_memory/REQUIREMENT_LEDGER.md | SATISFIED | CP-001 |
| R-016 | 00 §3 STATE B | MUST | Read 02_DISCOVERY_PROTOCOL.md upon entering STATE B | Read recorded before discovery work | this ledger | SATISFIED | Read at CP-001 staging (user directive); protocol active from STATE B start |
| R-017 | 00 §3 STATE B | MUST | Do not polish final documentation while architecture is still moving | docs/ absent until DESIGN_CONVERGED | repo tree | OPEN | |
| R-018 | 00 §3 STATE B | MUST | Write `DESIGN_CONVERGED` when convergence criteria are met | Marker file + commit | .v10/state/DESIGN_CONVERGED | OPEN | |
| R-019 | 00 §3 STATE C | MUST | Read 05, 05A, 05B, 06; extend ledger with packaging requirements; convert design to durable project memory | Ledger extension + knowledge package | docs/ | OPEN | Files staged in repo at CP-001 for recoverability (user directive); contracts become active at STATE C entry |
| R-020 | 00 §3 STATE D | MUST | Read 07, 08; perform cold-start, traceability, contradiction, visual/document consistency, transfer audits; every mandatory requirement satisfied or explicitly blocked with reason + recovery path; create final transfer ZIP | Audit report + ZIP | audit/, dist/ | OPEN | Same staging note as R-019 |
| R-021 | 00 §4 | MUST | Do not preload later instruction files; do not collapse package into one summary and discard sources | Progressive read order honored per state activation; mission/model/ preserved | mission/model/ | IN_PROGRESS | User-directed early staging for recoverability recorded in D-005 |
| R-022 | 00 §5 | MUST | Emit MISSION_COMPLETE only after Final Audit permits; otherwise MISSION_INCOMPLETE naming blockers | Final handoff statement | audit/ | OPEN | |
| R-023 | 00A §1–3 | MUST | Start immediately; no confirmation gates; no artificial pauses; continue to next highest-value required action | No voluntary stops in session | session record | IN_PROGRESS | |
| R-024 | 00A §4 | MUST | Blocker protocol: record in ledger + checkpoint; identify smallest recovery action; continue all independent work; stop only if all remaining work impossible | Blocker entries follow format | this ledger + CHECKPOINT.md | OPEN | None active |
| R-025 | 00A §5 | MUST | Tool-failure resilience: retry when reasonable, safe alternative, record limitation, continue unaffected work | Tool limitations logged | ACTIVE_NOTES.md | OPEN | Sandbox git push lacks credentials → GitHub API used (D-003) |
| R-026 | 00A §7 | MUST | Context-continuity: maintain working memory; checkpoint before context-heavy transitions; distill noisy notes; recover from canonical files | Checkpoint cadence | CHECKPOINT.md | IN_PROGRESS | |
| R-027 | 00A §8 | MUST | Self-repair loop: AUDIT FAILURE → REPAIR → RE-AUDIT until satisfied or genuinely blocked/documented | Repair log in audit report | audit/ | OPEN | |
| R-028 | 00A §9 | MUST | End only at MISSION_COMPLETE or MISSION_INCOMPLETE_HARD_BLOCK with required fields | Terminal statement | final handoff | OPEN | |
| R-029 | 00B §1 | MUST | Hard repository boundary: no read/inspect/search/clone/fetch/browse/compare/write of any other user repository | Process adherence; temptations logged if any | ACTIVE_NOTES.md | IN_PROGRESS | |
| R-030 | 00B §2 | MUST | Public research allowed but never as backdoor to user's prior FixHUA work | Source hygiene in index | SOURCE_INDEX.md | OPEN | |
| R-031 | 00B §3 | MUST | Repo eventually contains: root AGENTS.md; canonical project entry document; machine-readable state; ADRs; component contracts; implementation map; build/test/runbook; visual twin source; final audit; handoff checkpoint | All present and current | repo root | IN_PROGRESS | AGENTS.md/README/state at CP-001; rest in STATE C/D |
| R-032 | 00B §4 | MUST | Canonical working memory at .v10/working_memory/; distill at DESIGN_CONVERGED and before handoff; mark scratch; no secrets/keys/sensitive data (public repo) | Files exist; distillation markers; no secrets | .v10/working_memory/ | IN_PROGRESS | |
| R-033 | 00B §5 | SHOULD | Persist meaningful checkpoints: init/ledger; major architecture pivot; DESIGN_CONVERGED; package substantially complete; audit repaired+passed; final handoff | Commit history on main | repo commits | IN_PROGRESS | CP-001 = init |
| R-034 | 00B §6 | MUST | main is canonical branch; reconcile any temporary branch back before handoff | Branch list | repo | OPEN | |
| R-035 | 00B §7 | MUST | Contamination protocol: stop, record event, identify exposure, exclude exposed ideas as evidence, continue independently, disclose in final audit | Register entry if event occurs | CONTRADICTION_REGISTER.md / audit | OPEN | No event so far |
| R-036 | 00B §8 | MUST | Repository passes cold-start test (future model can determine conclusions, proposal, fact vs hypothesis, implemented vs specified, architecture, connections, build/test, unknowns, next task, safety rules) | Cold-start audit result | audit/ | OPEN | |
| R-037 | 00B §9 | MUST | Record final repository status proof before MISSION_COMPLETE (repo name, branch, SHA, package/version ID, cold-start result, unresolved blockers, next milestone) | Proof block | audit/ + handoff | OPEN | |
| R-038 | 01 §1 | MUST | Intellectual freedom: no choosing familiar merely for familiarity; no rejecting unfamiliar merely for unfamiliarity; may redefine problem/invent categories/combine fields | Option breadth in DECISION_LOG | DECISION_LOG.md | OPEN | |
| R-039 | 01 §2 | MUST | Risk protocol for high-impact risky ideas: hazard mechanism → intrinsic vs bounded dimension → redesign → research-only if unbounded; risk is engineering variable | Risk register entries follow protocol | RISK_REGISTER.md | OPEN | |
| R-040 | 01 §3 | MUST | Production admission: evidence, bounded scope, measurable effect, abort conditions, rollback, uncertainty awareness, validation path; never disable fundamental hardware safety protections; no account/banking/DRM/integrity/anti-tamper bypasses; no unrestricted arbitrary-root endpoints | Safety + admission contracts in design | docs/ (STATE C) | OPEN | |
| R-041 | 01 §4 | MUST | Models are not proof; modeled operating regions remain hypotheses until device evidence; uncertainty explicit | Truth states on claims | repo-wide | OPEN | |
| R-042 | 01 §5 | MUST | Causality before superstition: distinguish symptom/cause/dependency/consequence/coincidence; important causal claims have falsification paths | Experiment registry entries | EXPERIMENT_REGISTRY.md | OPEN | |
| R-043 | 01 §6 | MUST | Root expands search space; privileged controls must be observed → useful → safe before reliance | Control admission pipeline in design | docs/ | OPEN | |
| R-044 | 01 §7 | MUST | Every important subsystem justifies unique value, cost, overhead, risk, and why simpler mechanism insufficient | Justification sections | docs/ | OPEN | |
| R-045 | 01 §8 | MUST | Optimize real user-visible outcomes; free RAM / max clocks / synthetic scores / tweak counts are not success | Metric definitions tied to UX outcomes | docs/ | OPEN | |
| R-046 | 01 §9 | MUST | Truth-state discipline: consequential statements classified VERIFIED_FACT / IMPLEMENTED / TESTED / PROPOSED / EXPERIMENTAL / HYPOTHESIS / REJECTED / DEFERRED / UNKNOWN | Classifications present in state + docs | .v10/state/, docs/ | IN_PROGRESS | |
| R-047 | 01 §10 | MUST | No hidden omission of mandatory requirements | Completion lock check | audit/ | OPEN | |
| R-048 | 01 §11; 03 §8 | MUST | Working memory = concise operational notes; no private chain-of-thought externalization | Note style | .v10/working_memory/ | IN_PROGRESS | |
| R-049 | 03 §2 | MAY | Optional external memory surfaces allowed; continuity must not depend on them; no secrets; not described as private from user/service | — | — | NOT_APPLICABLE_WITH_JUSTIFICATION | None used; repo is the only surface |
| R-050 | 03 §3 | MUST | Update working memory on defined write events (fact, source, idea, hypothesis change, architecture change, superseded decision, contradiction, requirement status, experiment, risk change, tool limitation, ordering change) | File updates accompany events | .v10/working_memory/ | IN_PROGRESS | |
| R-051 | 03 §4 | MUST | Update CHECKPOINT.md on checkpoint events (architecture change, leaving research branch, experiments, tool/context-heavy branch, state switch, final packaging, continuity risk) | Checkpoint history | CHECKPOINT.md | IN_PROGRESS | |
| R-052 | 03 §5 | MUST | Recovery procedure: CHECKPOINT → LEDGER → DECISION_LOG → needed files; reconcile against artifacts before continuing | Procedure documented for successors | AGENTS.md | OPEN | |
| R-053 | 03 §6 | SHOULD | Memory hygiene: merge duplicates, archive obsolete, preserve IDs/provenance, mark superseded, keep ACTIVE_NOTES actionable | Notes remain scannable | .v10/working_memory/ | OPEN | |
| R-054 | 03 §7 | MUST | At DESIGN_CONVERGED distill validated facts, final decisions, rejected alternatives, uncertainties, evidence, implementation implications into Knowledge Package; scratch ≠ canonical architecture | Distilled docs differ from scratch | docs/ | OPEN | |
| R-055 | 04 §1 | MUST | Ledger entries atomic with source, priority, acceptance evidence, status, artifact, blockers; extend when later mission files load | This file's format; extensions logged | this ledger | IN_PROGRESS | |
| R-056 | 04 §2 | MUST | No silent prioritization; mandatory stays mandatory until resolved | All MUSTs tracked to terminal state | this ledger | OPEN | |
| R-057 | 04 §3 | MUST | Depth proportional to consequence, complexity, uncertainty, sensitivity, risk | Doc depth distribution | docs/ | OPEN | |
| R-058 | 04 §4 | MUST | Every MUST eventually points to concrete evidence (doc section, diagram, schema, test plan, decision record, blueprint, or explicit blocker) | Artifact column populated at closure | this ledger | OPEN | |
| R-059 | 04 §5 | MUST | Architecture-change recheck: reopen requirements whose evidence is invalidated; update decisions/notes | Recheck entries on change | this ledger + DECISION_LOG | OPEN | |
| R-060 | 04 §6 | MUST | Requirement conflicts logged with ID, exact conflict, why incompatible, resolution, sacrifice, whether clarification would change result | Conflict entries | CONTRADICTION_REGISTER.md | OPEN | None yet |
| R-061 | 04 §7 | MUST | Completion lock before MISSION_COMPLETE: every MUST SATISFIED or BLOCKED with precise reason + recovery path; no silently OPEN MUST | Final audit verification | audit/ | OPEN | |
| R-062 | 04 §8 | MUST | Use evidence statuses; never mark SATISFIED without evidence pointer | Status discipline | this ledger | IN_PROGRESS | |
| R-063 | 04 §9 | SHOULD | Compact ledger reviews at: post-init, major architecture change, DESIGN_CONVERGED, post-packaging, pre-ZIP | Review notes appended | this ledger | IN_PROGRESS | Post-init review = CP-001 |
| R-064 | 00B §9 / user directive | MUST | Persist canonical mission bootstrap/control files + initialized working memory to GitHub main before deep Discovery; GitHub alone sufficient for recovery after reset | CP-001 commits on main | repo main | IN_PROGRESS | Push in progress |

Statuses: OPEN / IN_PROGRESS / SATISFIED / PARTIALLY_SATISFIED / BLOCKED / SUPERSEDED / NOT_APPLICABLE_WITH_JUSTIFICATION

## Ledger review log
- CP-001 (STATE A init): 64 requirements compiled from 00/00A/00B/01/03/04 + user directive. R-013/R-014/R-015/R-016 SATISFIED with evidence. R-010 SATISFIED as standing duty. R-049 NOT_APPLICABLE_WITH_JUSTIFICATION. All others OPEN/IN_PROGRESS with owners. Next review: DESIGN_CONVERGED.
