# Closure Evidence Record

Status: **PENDING — ARCHIVAL IS NOT AUTHORIZED**

This record collects acceptance evidence for OpenSpec change `define-patient-gateway-and-escalation`. It does not replace the Speckit features' implementation tasks or validation artifacts.

## Governance Evidence

- Owner ratification reference: `owner-ratification.md` — _pending completion_
- Handoff shape ratified (A or B) and the resulting feature or change list: _pending_
- Design-call and tension dispositions as ratified: _pending_
- Stacked dependencies ratified (`define-mvp-practice-foundation`, `define-actors-agents-and-work-inbox`, and the FD-1 and FD-10(b) amendments): _pending_
- Constitution version and per-feature Constitution Check evidence: _pending_
- Per-feature security review records and risk-analysis updates: _pending_
- Speckit feature identifiers and paths (patient gateway; escalation routing and clinical handoff; payment hold; mobile approver): _pending_

## Product Acceptance Evidence

- Requirement and scenario traceability for `patient-app-gateway`: _pending_
- Requirement and scenario traceability for `escalation-routing`: _pending_
- Requirement and scenario traceability for `payment-hold`: _pending_
- Requirement and scenario traceability for `mobile-approver`: _pending_
- Contract tests for `openpractice-patient-gateway/1.0` and `openpractice-clinical-handoff/1.0`, validated against the canonical schemas at their recorded digests (`contracts/SHA256SUMS`): _pending_
- The three always-granted capabilities present in every entitlement result, including for inactive patients, held patients and evaluation failures: _pending_
- Gateway links only after verified identity and a named human's confirmation; no auto-linking; agent sign-off refused; no master identity: _pending_
- Representative links refused without an approved proxy policy: _pending_
- Patient proposals never auto-applied; accepted changes audited and published with patient provenance; interim confidential-communication restrictions honored: _pending_
- Clinical inquiries carrying text refused; no clinical content in any routing record or handoff: _pending_
- Urgency never lowered by rule, staff, agent or later proposal: _pending_
- Acknowledgment, shadow response deadline and every alert trigger, with the office manager alerted: _pending_
- Identity state on every handoff; urgent and priority items offered as `link_in_review` or `unlinked` (three snapshot fields only), routine items on an uncertain link kept as exceptions: _pending_
- Clinician urgency raises: recorded, acknowledged, deadline shadowed, urgent path run, urgency never lowered: _pending_
- Patient notices and `notice.ack`; hold countdowns and grace periods start only after a confirmed or recorded delivery: _pending_
- HealthLinc operator agreement verified before any gateway capability; refusal with `operator_agreement_unverified` meanwhile: _pending_
- Urgent fallback (office manager, on-call clinician, HealthLinc guidance instruction, retries), including on `clock_expired` and silence past `respond_by` with cause `clinical_response_overdue`, and the no-on-call fallback to the medical director: _pending_
- Tracked alerts: delivery, acknowledgment, re-sending and escalation to the medical director; the priority alert: _pending_
- Rejected urgent inquiries (link, authorization or `pending_review` reasons) raise a PHI-free exception and a guidance instruction: _pending_
- Clinical and urgent intake refused with `clinical_routing_unavailable` while clinical routing is missing or disabled: _pending_
- Ruling 50's safeguards for routine clinical messaging and medical-AI consults: _pending_
- `medical_ai_consult` gated like non-urgent clinical messaging, and HealthLinc's coach never gated: _pending_
- Conversation source and reference for HealthLinc-held and medical-AI conversations, with no conversation content in openPractice: _pending_
- `proposal_source` on every inquiry, and the red-flag checklist's `urgent`, `routine` and `priority` proposals applied and never lowered: _pending_
- The clinical-need marker: attribution, `valid_until`, the urgent-origin check, and no openPractice-created marker: _pending_
- `representative_scope` in place of a hold for a representative without billing scope: _pending_
- A linked representative's relationship and authority reference in `link.result` and in the assertion (PG-06): _pending_
- Entitlement assertions (PG-16): registered relying parties only, every required property, no PHI or hold, verification and refusal by a relying-party double, and urgent access unchanged without one: _pending_
- `clinician_acknowledged` (ER-19): recorded, acknowledged, passed to HealthLinc, never inferred, and changing no safety path: _pending_
- Deletion of misrouted text from the agent layer and model provider under the AH-02 agreements: _pending_
- Hermes drafts at `propose` only, decided by a human in the lane and never by Hermes; read-only answers only under change A's AH-05 exception 1; no drafted-reply or hold action type before the agent-operations MINOR: _pending_
- Staff answers only in their lane; no openPractice answer to a clinical item: _pending_
- Re-routing and splitting with stored-text removal and a PHI-free removal event: _pending_
- Pause switch and Hermes unavailability send administrative items to staff with no loss: _pending_
- Payment hold: only three capabilities withheld; scheduling clause only with a ratified scheduling change; clinician marker and verified urgent origin honored; policy gate; tiers and agent ceilings; patient notice before effect; disputed amounts and GFE windows excluded; no claim stalled; automatic lift: _pending_
- Mobile approver: versioned API only; no PHI in notifications or on the device; re-authentication for deep links and sign-offs; lost-device revocation; tier 4 human-only and D8 accountant-only: _pending_
- No PHI in logs, errors, alerts, push text, idempotency keys or diagnostics: _pending_

## Boundary and Merge Evidence

- Final openPractice changed-file and dependency inventory: _pending_
- No-cross-repository-runtime confirmation (HealthLinc, openChart, MedxFactory untouched): _pending_
- Cross-repository contract consistency with each companion's landed implementation, or a pending note: _pending_
- Merged leg PR identifiers, exact heads, and the assembly root pin-bump PR: _pending_
- Required checks, review disposition and validation summary: _pending_

## Final Validation and Archive Evidence

- Final strict OpenSpec validation command, version, timestamp, revision, exit code and output: _pending_
- Delta-spec sync disposition: _pending_
- Schemas moved to the spec repository's `contracts/` directory with digests unchanged: _pending_
- Archive date and path: _pending_

Until every applicable field is complete, every delta requirement and scenario is reconciled against merged implementation, and final strict validation exits 0, this change must remain active.
