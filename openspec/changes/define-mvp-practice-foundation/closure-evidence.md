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
- Requirement and scenario traceability for `practice-onboarding`: [Feature 5 evidence](#feature-5--practice-onboarding).
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
- Sign-offs per FD-5 tiers: agent signing opt-in only, refused at tier 4, recorded with the openWallet ID (BAB-17, ruling 33): _pending_
- Summary-journal minimum group size and roll-up, and automatic close re-estimates (AI-15, AI-12): _pending_
- Bank register, deposit links, expected-account check, re-enrollment worklist, and encrypted, role-restricted, audited bank numbers: _pending_
- Date-of-birth hold suspension, correction requests and single-resolver identity review: _pending_
- Messages validated against canonical schemas at their recorded digests (`contracts/SHA256SUMS`): _pending_
- Hard-delete removal scope (live data and audit values, PHI-free tombstone, tamper-evident redaction evidence), and verification flagging any audit value missing without a redaction event: _pending_

### Feature 5 — Practice Onboarding

**PracticeLab QA onboarding complete (2026-11-27).** AKS browser job `practicelab-onboarding-browser-cc435355` passed on the public New Practice page using synthetic practices. The browser used normal TLS verification, and the page redirected signed-out users to sign-in. The hosted run used openPractice source `a114558fb97567079b56dc0f2f9452775d04638b`. [PracticeLab New Practice](https://practicelab.opensoft.dev/new-practice) · [Public HTTPS rollout](https://github.com/opensoft/Opensoft-Tenant/pull/474) · [Onboarding rollout](https://github.com/opensoft/Opensoft-Tenant/pull/476)

| Scenario | Requirement | Observed result |
| --- | --- | --- |
| Solo create | PO-01, PO-02 | Saved one office and one legal entity as Single practitioner, created its openHR Company, and showed the saved Company identifier after reload. |
| Existing group link | PO-01, PO-02 | Saved one office and one legal entity as Group, linked the existing openHR Company, and showed the same identifier after reload. The native Company record remained unchanged. |
| Lost-response retry | PO-03 | The test dropped the response after openHR committed the create. New Practice showed the unresolved connection. Retry recovered the original Company identifier and showed it as connected after reload. Independent native openHR readback found **exactly one Company** by legal entity name and exactly one by stable request ID. Both matched the recovered identifier. |

**Overlapping create submissions passed (2026-11-27, PO-03).** Two authenticated requests for the same synthetic setup overlapped for 1.43 seconds over PracticeLab public HTTPS. Both original responses returned HTTP 200 and `Company connected` with Company ID `Synthetic Overlap 187bf62594c1 LLC`. The saved setup showed the same identifier. Independent native openHR readback found **exactly one Company** by legal entity name and exactly one by stable request ID. Both matched the identifier in the responses. The run used source `8cfd852c3a287557db18d6439f488e8d2e71b8a1` after Flux applied `27f305194781983c58acf77ec8ea5340fe8234c5` to the QA workloads. AKS command `fa76dd165fad422fbaf6f6fe3fca0b1a` captured the readback. [QA rollout](https://github.com/opensoft/Opensoft-Tenant/pull/479) · [Source commit](https://github.com/opensoft/openPractice-code/commit/8cfd852c3a287557db18d6439f488e8d2e71b8a1)

The connector unit test `test_an_unresolved_retry_does_not_issue_another_create` passed at both deployed revisions. It verifies that a retry makes no second create when reconciliation finds no Company.

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
