# Closure Evidence Record

Status: **PENDING — ARCHIVAL IS NOT AUTHORIZED**

This record collects acceptance evidence for OpenSpec change `define-mvp-practice-foundation`. It does not replace the Speckit features' implementation tasks or validation artifacts.

## Governance Evidence

- Owner ratification reference: `owner-ratification.md` — _pending completion_
- Handoff shape ratified (A or B) and the resulting feature or change list: _pending_
- Constitution version and per-feature Constitution Check evidence: _pending_
- Per-feature security review records and risk-analysis updates: _pending_
- Speckit feature identifiers and paths (patient registry; EHR patient link; accounting contract and ERPNext adapter; clinical-fact consumption): _pending_
- Evidence that the Thread contract (FD-7) and reason taxonomy were versioned before the accounting feature started: _pending_

## Product Acceptance Evidence

- Requirement and scenario traceability for `patient-registry`: _pending_
- Requirement and scenario traceability for `ehr-boundary`: _pending_
- Requirement and scenario traceability for `billing-accounting-boundary`: _pending_
- Requirement and scenario traceability for `accounting-integration`: _pending_
- Administrative sex, gender identity and pronoun independence, with no payer leakage: _pending_
- Deactivation semantics, and hard-delete refusal on linked activity and active holds: _pending_
- Every change and every PHI read audited, with no PHI in logs: _pending_
- Demographic outbox atomicity, idempotent and version-ordered acknowledgements, and identity review without merge: _pending_
- Contract tests for `openpractice-ehr-patient-link/1.0` and `openpractice-ehr-clinical-facts/1.0`: _pending_
- Contract tests for `openpractice-accounting-integration/1.0` (all operations, replay, indeterminate lookup, batch freeze, release refusals, reconciliation): _pending_
- openPractice installed and tested with no ERPNext present; ERPNext adapter verified over REST against the remote topology (ruling 8): _pending_
- Summary-journal no-PHI verification and customer-detail minimum-necessary and recipient-profile verification: _pending_
- BAA gate refusal (including operator attestation), tier-4 enablement and halt-on-lapse verification: _pending_
- Mode lock after first release; mapping versioned per destination and mode; `summary-journal` as the default with payer-class control-account reconciliation (ruling 28): _pending_
- Constrained-estimate revenue, evidence true-ups, close re-estimate dating and close snapshots (ruling 30): _pending_
- Accountant approval of the reporting basis, estimator, classification, materiality and reopening policy, recorded before any destination went live (ruling 30, D8): _pending_
- Customers keyed by guarantor account number and named only in customer-detail mode behind the BAA gate, with the recipient-profile version recorded (ruling 25); neutral labels; accounting dates, including event dates for payments, refunds and deposits; late-charge flag: _pending_
- Single deposit poster with `other_route_share` lines, refund-disbursement matching, and the offering-route check before charge finalization (BAB-14, BAB-16): _pending_
- Manual entry under identity review only with a recorded attestation, and refusal without it (EB-17): _pending_
- Retractions with `entered_in_error` or `withdrawn` handled alike, no cause inferred or shown, undefined codes applied as `withdrawn`, retractions applied only to facts from the same link and subject, and retractions accepted after a link ends (EB-15; rulings 87 to 89): _pending_
- Sign-offs per FD-5 tiers: agent signing opt-in only, refused at tier 4, recorded with the openWallet ID (BAB-17, ruling 33): _pending_
- Summary-journal minimum group size and roll-up, and automatic close re-estimates (AI-15, AI-12): _pending_
- Bank register, deposit links, expected-account check, re-enrollment worklist, and encrypted, role-restricted, audited bank numbers: _pending_
- Date-of-birth hold suspension, correction requests and single-resolver identity review: _pending_
- Messages validated against canonical schemas at their recorded digests (`contracts/SHA256SUMS`): _pending_
- Hard-delete removal scope (live data and audit values, PHI-free tombstone, tamper-evident redaction evidence), and verification flagging any audit value missing without a redaction event: _pending_

## Boundary and Merge Evidence

- Final openPractice changed-file and dependency inventory, confirming no core import of ERPNext or any vendor library: _pending_
- No-cross-repository-runtime confirmation: _pending_
- Cross-repository contract consistency with openChart's landed implementation, or a pending note: _pending_
- Merged leg PR identifiers, exact heads, and the assembly root pin-bump PR: _pending_
- Required checks, review disposition and validation summary: _pending_

## Final Validation and Archive Evidence

- Final strict OpenSpec validation command, version, timestamp, revision, exit code and output: _pending_
- Delta-spec sync disposition: _pending_
- Confirmation that the post-MVP QuickBooks Online and Business Central adapters were excluded from closure: _pending_
- Archive date and path: _pending_

Until every applicable field is complete, every delta requirement and scenario is reconciled against merged implementation, and final strict validation exits 0, this change must remain active.
