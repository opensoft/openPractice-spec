# Closure Evidence Record

Status: **PENDING — ARCHIVAL IS NOT AUTHORIZED**

This record collects acceptance evidence for OpenSpec change `define-practice-readiness`. It does not replace the Speckit feature's implementation tasks or validation artifacts.

## Governance Evidence

- Owner ratification reference: `owner-ratification.md` — _pending completion_
- Handoff shape ratified, and the Speckit feature identifier and path: _pending_
- Design-call, tension and amendment dispositions as ratified: _pending_
- Dependencies merged (PR #7 and the corrections change, PR #8), with revisions: _pending_
- Constitution version and Constitution Check evidence: _pending_
- Security review record and risk-analysis update: _pending_

## Product Acceptance Evidence

- Requirement and scenario traceability for `readiness-checklist`, `policy-starter-kit` and `signer-roles`: _pending_
- The checklist reads each row from the gate's own record, offers no write path, and enables nothing when every row is complete (RC-03): _pending_
- Every row traced to its enforcing requirement; no row without one; a row whose requirement names no authority says so (RC-02): _pending_
- Row states, with `unavailable` when a gate's record cannot be read and `not_required` only from the requirement's own condition (RC-04): _pending_
- What stays off and what still runs, in the enforcing requirement's words (RC-05): _pending_
- Authority holders, one-person marks, collapse warnings, and the list of one-person uses and both-sides sign-offs, with every use whose subject is a patient-side record shown only as a count with an opaque reference, both visible only to readers authorized to see that PHI (RC-06): _pending_
- No PHI in the checklist, its logs or its diagnostics; read only through the versioned API (RC-07): _pending_
- The starter kit's templates, none of them read by a gate; inert after installation and upgrade (SK-01, SK-02): _pending_
- A version made from a template approved through the gate's own action with the same record as one written from scratch, and template provenance that changes no check (SK-03): _pending_
- No adoption by default, no bulk adoption or approval, one opt-in per agent and action type (SK-04): _pending_
- Immutable template versions; a newer or withdrawn template changes no policy in force (SK-05): _pending_
- Templates carry no legal, accounting or clinical rule; the retention template holds no duration (SK-06): _pending_
- The role on every sign-off; refusal of a role not held or not allowed; two sign-offs for two roles (SR-01, SR-02): _pending_
- Fail-closed handling of a two-person rule with no one-person alternative, or an unavailable one; each one-person use recorded (SR-03): _pending_
- Every SR-04 refusal: no outside engagement, an employed accountant, an accountant's entry not created with its arrangement or holding another recipe, no own attestation, or one that does not confirm the owners recorded, no CPA licence or one that does not check active against the state board's public record, a counsel admission that does not check active against the state bar's public record, a name matching another roster party or a recorded owner, no owner link recorded, the other side of the same item, an agent; and no SR-04 check outside the sign-offs it names: _pending_
- The practice manager as "an authorized person" where a requirement names no other role, with the role kept on each record (SR-02, ruling 142): _pending_
- Each shipped template version with counsel's, an accountant's and a physician's reviews recorded (SK-06, ruling 147): _pending_

## Boundary and Merge Evidence

- Final openPractice changed-file and dependency inventory: _pending_
- No-cross-repository-runtime confirmation: _pending_
- Rows re-verified against the requirements at the landed revision: _pending_
- Merged leg PR identifiers, exact heads, and the assembly root pin-bump PR: _pending_
- Required checks, review disposition and validation summary: _pending_

## Final Validation and Archive Evidence

- Final strict OpenSpec validation command, version, timestamp, revision, exit code and output: _pending_
- Delta-spec sync disposition: _pending_
- Archive date and path, after the feature landed (ruling 109): _pending_

Until every applicable field is complete, every delta requirement and scenario is reconciled against merged implementation, and final strict validation exits 0, this change must remain active.
