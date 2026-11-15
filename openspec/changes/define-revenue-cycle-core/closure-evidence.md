# Closure Evidence Record

Status: **PENDING — ARCHIVAL IS NOT AUTHORIZED**

This record collects acceptance evidence for OpenSpec change `define-revenue-cycle-core`. It does not replace the Speckit features' implementation tasks or validation artifacts.

## Governance Evidence

- Owner ratification reference: `owner-ratification.md` — _pending completion_
- Hand-off shape ratified, and the resulting feature list: _pending_
- Dispositions of PA-1 to PA-11, with the changes and revisions that carry them: _pending_
- Constitution version and per-feature Constitution Check evidence: _pending_
- Per-feature security review records and risk-analysis updates: _pending_
- Speckit feature identifiers and paths (foundations; self-pay visit; claims against a simulated payer; remittance posting and full balances; live clearinghouse and Medicare adapter; payment processor and pay link): _pending_
- Evidence that each feature started only after its gate in `tasks.md` section 2 was met, including the claim-pathway, remittance and payment-processor contracts defined by governed amendments before features 3, 4, 5 and 6 (CR-24): the claim-pathway contract before feature 3, the remittance contract before feature 4, the clearinghouse contract before feature 5, and the payment-processor contract before feature 6: _pending_
- Tiers proposed in feature plans for recording actions no requirement tiered, and the owner's disposition (`tasks.md` 3.5): _pending_

## Product Acceptance Evidence

- Requirement and scenario traceability for `encounter-thread`: _pending_
- Requirement and scenario traceability for `administrative-appointment`: _pending_
- Requirement and scenario traceability for `service-catalog-and-pricing`: _pending_
- Requirement and scenario traceability for `charge-capture`: _pending_
- Requirement and scenario traceability for `money-subledger`: _pending_
- Requirement and scenario traceability for `reason-taxonomy`: _pending_
- Requirement and scenario traceability for `revenue-estimate`: _pending_
- Requirement and scenario traceability for `good-faith-estimate`: _pending_
- Requirement and scenario traceability for `patient-balance-and-statements`: _pending_
- Requirement and scenario traceability for `patient-payments-and-refunds`: _pending_
- Requirement and scenario traceability for `coverage-eligibility-evidence`: _pending_
- Requirement and scenario traceability for `participation-gate`: _pending_
- Requirement and scenario traceability for `claim-lifecycle`: _pending_
- Requirement and scenario traceability for `claim-pathway-adapter`: _pending_
- Requirement and scenario traceability for `remittance-posting`: _pending_
- Contract tests for `openpractice-thread/1.0` at its recorded digest, with every legal move tested and every other move refused: _pending_
- Every ledger entry balanced, never edited, reversed for correction, and answerable as of a past date; whole cents and one rounding rule: _pending_
- Summary-journal batches with this change's revenue refused until the service-category field existed, then carrying it with its mapping version, and payer-share transfers carrying both payer classes (RT-04, PA-1): _pending_
- Estimator versions and the policy record approved only by the practice's accountant, never self-approved, before any destination went live (RE-02, RE-09): _pending_
- Codes from EHR facts or marked manual, confirmed by a named biller, never inferred (CG-02, CG-04): _pending_
- No Good Faith Estimate issued without a counsel-verified parameter set, the practice issuing estimates outside the system until then (ruling 172); delivery proved; disputes marking money disputed (GF-03, GF-07, GF-10): _pending_
- Claim batches released only by a named signer, with the escape refusing retracted facts and denied or unknown participation (CL-05, CL-06, EG-06): _pending_
- Exactly matched remittances posted with notice and undo, only under the conditions ruling 169 accepts, and undone plainly only until matched to a bank deposit (ruling 170); everything else proposed to a human; a patient's share waiting for the second insurance and denials never written off automatically (ruling 171), a denied amount staying in the payer receivable until a named human appeals it, writes it off, or, where the payer's answer shows the patient liable, moves it to the patient with a recorded reason, and the contractual adjustment taken only from amounts classified as contractual (RM-04, RM-05, RM-07, RT-05): _pending_
- Money that arrived before its remittance cleared exactly from unidentified receipts by the remittance's posting as a whole, including a remittance that nets payer-level adjustments, one paid by several deposits, and a receipt linked after its remittance posted (RM-01, RM-08): _pending_
- Refunds and material write-offs signed by a named human, every write-off by a named human until the material amount is set (ruling 173), and transfers between guarantor accounts by a named human with a notification (ruling 174); the twelve recording actions of ruling 182 done by a named human with a notification, snoozing without one, until the owner rules on each; agent ceilings of ruling 132 enforced (PY-07, PS-11, PS-12, TH-13): _pending_
- No card or bank number stored; no PHI in logs, keys or diagnostics; synthetic fixtures only (PY-01, RM-01, TH-15, TH-16): _pending_
- The old-system balance import built in feature 4, or earlier where the first practice had old balances, before that practice went live (SL-11, rulings 131 and 176): _pending_
- FD-8 beta acceptance: one live electronic remittance posted end to end, and one live merchant settlement reconciled to a bank line: _pending_

## Boundary and Merge Evidence

- Final openPractice changed-file and dependency inventory, confirming that the core imports no adapter, ERPNext or vendor library (CP-08): _pending_
- No-cross-repository-runtime confirmation: _pending_
- Merged leg PR identifiers, exact heads, and the assembly root pin-bump PR: _pending_
- Required checks, review disposition and validation summary: _pending_

## Final Validation and Archive Evidence

- Final strict OpenSpec validation command, version, timestamp, revision, exit code and output: _pending_
- The thread contract's canonical schema moved to the spec repository's `contracts/` directory with its digest unchanged: _pending_
- Delta-spec sync disposition: _pending_
- Archive date and path: _pending_

Until every applicable field is complete, every delta requirement and scenario is reconciled against merged implementation, and final strict validation exits 0, this change must remain active.
