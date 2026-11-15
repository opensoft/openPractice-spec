## 1. Owner Ratification Gate

- [ ] 1.1 Run `openspec validate define-revenue-cycle-core --strict --no-interactive` against the exact artifacts proposed for ratification. Acceptance evidence: `owner-ratification.md` records the command, the OpenSpec version, the timestamp, the exact artifact revision or checksums, exit code 0 and the output.
- [ ] 1.2 Obtain the owner's unconditional ratification before any Speckit feature is created. On 2026-11-15 the owner ruled for one change with its own ordered multi-feature hand-off (ruling 112), as a stated exception to the workflow protocol's one-feature hand-off, as ruling 1 did for `define-mvp-practice-foundation`. The ratification record must still confirm that choice. Acceptance evidence: the owner, not an agent, completes `owner-ratification.md`: identity, timestamp, exact revision, strict-validation evidence, the confirmed hand-off shape, a disposition of each design call (CR-1 to CR-55) and tension (T-1 to T-17), and the unqualified decision `APPROVED UNCONDITIONALLY`. In the same revision, the owner changes the proposal's `Status` to `ratified`.
- [ ] 1.3 Confirm the dependencies have merged and still say what this change relies on. Draft PRs opensoft/openPractice-spec#7, #8 and #10 must be merged. Every requirement and design-call ID this change cites from them (EB-13, EB-15, PR-15, F-7, BAB-18, F-8 to F-10, AM-01, AO-04, AO-07, SR-02, SR-04, RD-11, I-1 and RI-51) must exist in the merged text, and must still mean what `design.md` "Dependencies" describes. PR #8 was re-checked at `050bdfd` and PR #10 at `d4724e1`, and both were still moving, so their final text must be re-read. Acceptance evidence: each merged revision, and the result of the cross-reference check, recorded in `owner-ratification.md`. A missing ID or a changed meaning blocks feature creation until this change is amended.
- [ ] 1.4 Verify the thread contract's canonical schema. Acceptance evidence: `sha256sum -c contracts/SHA256SUMS` run in this change directory, its output, and the digest `6a155eb93d6aece04bd6f730304467b1227a7c8af4040b58bd6780e887d579bf` for `openpractice-thread-1.0.schema.json`, recorded in `owner-ratification.md`.
- [ ] 1.5 Record a disposition for each proposed amendment, PA-1 to PA-6, from the owner of the change it would alter. Acceptance evidence: each one accepted, changed or rejected, with the change and revision that carries it, recorded in `owner-ratification.md`. PA-1 does not block feature creation. It must be adopted before any summary-journal batch carries a revenue or deduction line from this change (RT-04), so it blocks the go-live of `define-mvp-practice-foundation` Feature 3 for this change's revenue.

## 2. Speckit Features That May Follow, In Order (Ruling 112)

Each feature is gated on task 1.2 and is created from the assembly root `opensoft/openPractice` on its base branch, in the order below. A feature's gate lists what must have merged before it starts. Every feature is accepted against synthetic data only (TH-16). A real practice's money first moves through openPractice at beta acceptance, which FD-8 sets as one live remittance posted end to end and one live merchant settlement reconciled to a bank line. That needs features 5 and 6, and the foundation's deposit explanation (BAB-14).

The foundation change's four features slot in as `design.md` Decision 1 sets out: its Feature 1 (patient registry) comes before feature 1 below; its Feature 2 (EHR patient link) may run alongside features 1 and 2; its Feature 3 (accounting contract and ERPNext adapter) follows feature 1 and, for go-live, PA-1; and its Feature 4 (clinical-fact consumption) is folded into feature 3 below, as its task 2.4 allows (PA-3).

- [ ] 2.1 **Feature 1: foundations: the visit thread, the money ledger, reason codes and the revenue-estimate record.** Owns TH-01 to TH-16, SL-01 to SL-12, RT-01 to RT-08 and RE-01 to RE-09. Gate: task 1.2, and `define-mvp-practice-foundation` Feature 1 (patient registry) merged, because the ledger's counterparty accounts reference the guarantor account, the patient and the coverage version (SL-06). Acceptance evidence:
  - contract tests validate every thread snapshot and transition against `contracts/openpractice-thread-1.0.schema.json` at its recorded digest; every move in TH-04's table is tested, and every move it does not list is refused, including a rewind;
  - each hold reason and its release condition, each case type with its minting event, the derived clock and amount, and re-association with its 30-day undo, are tested (TH-03, TH-06, TH-08, TH-10);
  - every ledger entry balances; entries are never edited, and a correction is a reversal; any balance is answerable as of a past date; amounts are whole cents with one rounding rule and cumulative rounding for splits (SL-01, SL-04, SL-05, BAB-18);
  - each of the 38 kinds posts its bucket pair and neutral label, and a revenue entry carries its service category (RT-02, RT-03);
  - a summary-journal batch carrying a revenue or deduction line from this change is refused while the accounting contract has no service-category field (RT-04, PA-1);
  - an estimate record is created for each final charge line, including one awaiting an estimator; approval of an estimator version is refused for an agent, for the owner as such, and for a self-approval; close re-estimates run with no approval step (RE-01, RE-02, RE-06);
  - the `old_system_balance` case type and the `legacy_collections` bucket exist as record types; the import is not part of this feature (SL-11, ruling 131);
  - the action types of TH-13 are added to `openpractice-agent-operations` as a MINOR version, each starting at `propose`, and a rung above its ruling-132 ceiling is refused (AO-11);
  - the ledger and transition audit is tamper-evident, with no PHI in logs, keys or diagnostics (SL-12, TH-15);
  - the PHI safeguard plan, security review and risk-analysis update are recorded.
- [ ] 2.2 **Feature 2: the self-pay visit, end to end. The first buildable slice (ruling 114).** Owns SC-01 to SC-08; AP-01 to AP-09; CG-01 to CG-09 for manual entry where the practice has no linked EHR (the first case of EB-17); GF-01 to GF-12; PS-01 to PS-09 and PS-11, for self-pay balances and printable statements; and PY-01 to PY-05 and PY-11. Gate: task 1.2, and feature 1 merged. It needs no payer, clearinghouse or processor. Acceptance evidence:
  - booking, rescheduling, cancelling, no-shows and walk-ins each move the thread, and none deletes an appointment; booking applies PH-01's clause with its exceptions (AP-01 to AP-07);
  - prices are read by date of service; the self-pay discount applies only as SC-04 states; no payer fee schedule is an input (SC-03, SC-04, SC-07);
  - manual codes are marked manual with their reason; a biller's confirmation is required before a charge is final, and a self-confirmation on the biller's own record follows PR-15's rule; nothing infers a code (CG-02, CG-04);
  - a final self-pay charge posts `service_self_pay` and `self_pay_discount`, and the estimate record matches (CG-09, RE-01, RE-03);
  - with no counsel-verified Good Faith Estimate parameter set in force, no estimate is issued and the booking raises work while the visit goes ahead; with a synthetic verified set, issue, replacement, proved delivery, retention, variance and a dispute that marks the money disputed are all tested (GF-03 to GF-10);
  - desk payments post to the right bucket before and after the service; receipts are frozen; allocation follows PY-04; the deposit slip lists exactly the desk money taken to the bank (PY-01 to PY-05);
  - printed statements are frozen, honor every communication restriction, carry no clinical content, and leave disputed money off (PS-03 to PS-07);
  - a write-off before the material amount is set needs a named human (PS-11);
  - a payment hold stops no payment and no statement (PS-08, PY-11);
  - security review and risk-analysis update recorded.

  Until feature 4 lands, money a patient has overpaid stays an unapplied credit owned as work (PY-04), and no refund is recorded in openPractice. That is acceptable only because no real practice's money moves before beta acceptance.
- [ ] 2.3 **Feature 3: claims against a simulated payer, with the participation hold and eligibility evidence.** Owns CL-01 to CL-13; CP-01 to CP-04 and CP-07 to CP-09; EG-01 to EG-07; EV-01, EV-02 and EV-04 to EV-06; the EHR-facts branch of CG-02, with CG-05 and CG-08; and, folded in from `define-mvp-practice-foundation`, its Feature 4: EB-13 to EB-18 (PA-3). Gate: task 1.2; feature 2 merged; `define-mvp-practice-foundation` Feature 2 (EHR patient link) merged; the claim-pathway adapter contract defined by a governed amendment of this change (CR-24); and the single feature of `add-reimbursement-retention-control` merged, or its protected-operation and outbox contracts fixed, because every submission goes through them (CL-11; T-13). Acceptance evidence:
  - Medicare Part B primary claims are built from final charge lines only, scrubbed, held with the hold reason naming any failure, and released only by a named signer's batch release (CL-03 to CL-05);
  - the filing-deadline escape is tested, including its refusal for a retracted fact and for denied or unknown participation (CL-06, EG-06);
  - claims as submitted are frozen; a replacement or void needs the original's payer control number (CL-07, CL-08);
  - claims to any other payer, and secondary claims, are recorded through the manual path and move the receivable exactly as a built claim does (CL-02, CP-04);
  - acceptance moves the receivable from unbilled to billed, and sending or rejection posts nothing (CL-12);
  - the simulated payer refuses any payer identity not marked synthetic; the core holds no payer or jurisdiction rule (CP-02, CP-03);
  - a claim whose provider is not enrolled is held, never rejected, and released in one action once enrolled; a released claim still needs the batch release (EG-03, EG-04);
  - eligibility and authorization evidence is recorded by hand and never edited; missing evidence holds the claim, never the patient (EV-01, EV-02, EV-04, EV-06);
  - contract tests run against the clinical-facts canonical schema at its recorded digest, including retractions, and the EB-17 attestation path with refusal without it (EB-13 to EB-18, CG-05, CG-08);
  - adapters are separate packages that the core never imports; adapter tests are contract tests with synthetic fixtures (CP-08, CP-09);
  - security review and risk-analysis update recorded.
- [ ] 2.4 **Feature 4: remittance posting, and full patient balances, credits and refunds.** Owns RM-01 to RM-11; PS-01 to PS-09 and PS-11 for insured balances (the patient's share after the payer answers, and appeals that mark it disputed); and PY-06 to PY-09. It also builds the old-system balance import (SL-11, ruling 131), unless the first practice that needs it goes live sooner, in which case the import moves to the feature before that go-live. Gate: task 1.2; feature 3 merged; and the remittance contract defined by a governed amendment of this change (CR-24). Acceptance evidence:
  - paper and electronic remittances produce the same records; a remittance and its payment are separate records, and the payment carries a trace reference, never a bank number (RM-01, RM-02);
  - a claim payment attaches only on an exact match; an exactly matched payment posts with notice and can be undone until its deposit is explained; anything else is a proposal for a human (RM-03 to RM-05);
  - posting realizes the contractual adjustment, trues up the estimate, records the payment and moves the patient's share, which waits where a later coverage exists; a denied amount is never written off by itself (RM-07);
  - a remittance that does not add up does not post; takebacks, recoupments and reversals post as new entries with links (RM-08 to RM-10);
  - credit-balance cases carry their clocks, and with no counsel-verified clock parameter the case is due at once; refunds leaving the practice are tier 4 and recorded only when issued; a staff member's own refund follows PR-15's rule; a chargeback or returned item reopens what it paid (PY-06 to PY-09);
  - remittance detail is readable only by roles that need it, and each read is a use event (RM-11);
  - security review and risk-analysis update recorded.
- [ ] 2.5 **Feature 5: the live clearinghouse and the Medicare adapter.** Owns CP-05, CP-06 and EV-03. Gate: task 1.2; feature 4 merged; the clearinghouse's business associate agreement verified and recorded (CP-05); and the certified biller's answers on the Medicare jurisdiction, contractor, enrollment and submission channel (`design.md` Open Question 25). Acceptance evidence:
  - the clearinghouse adapter cannot activate without a verified agreement;
  - the Medicare adapter keeps every jurisdiction rule out of the core (CP-02, CP-06);
  - electronic eligibility responses are kept exactly as received, with their normalized result (EV-03);
  - FD-8's first condition: one live electronic remittance posted end to end;
  - security review and risk-analysis update recorded.
- [ ] 2.6 **Feature 6: the payment processor and the pay link.** Owns PY-10, with the electronic statement delivery of PS-04 and the delayed sending of PS-10. Gate: task 1.2; feature 4 merged; and the payment-processor contract defined by a governed amendment of this change (CR-24). Acceptance evidence:
  - no card or bank number is stored in openPractice;
  - statements sent from openPractice wait out a cancel window, and honor every communication restriction (PS-06, PS-10);
  - FD-8's second condition: one live merchant settlement reconciled to a bank line, through the foundation's deposit explanation (BAB-14);
  - security review and risk-analysis update recorded.

## 3. Planning Gates For Every Feature

- [ ] 3.1 Perform a meaningful Constitution Check against constitution v1.0.0 before each feature's plan phase. Acceptance evidence: the plan maps Principles I to VI and the quality gates to concrete obligations, and records the open questions routed to it in 3.5.
- [ ] 3.2 Complete a documented security review and update the living risk analysis for each feature. Acceptance evidence: the review record and the risk-analysis diff are referenced from `closure-evidence.md`.
- [ ] 3.3 Confine runtime changes to the openPractice code leg. Acceptance evidence: the plan's inventory shows no runtime edits in openChart, ERPNext, a clearinghouse, a processor or any other vendor system.
- [ ] 3.4 Keep this checklist governance-only. Acceptance evidence: executable tasks exist only in each feature's Speckit `tasks.md`, and `speckit-analyze` reports no unresolved critical inconsistency.
- [ ] 3.5 Give each recording action whose tier no requirement states a tier in the feature's plan, for the owner's review (`design.md` "Governance tiers"). Acceptance evidence: the plan names the action, the proposed tier and why; agents stay at `propose` for it meanwhile (TH-13).
- [ ] 3.6 Route every open question (`design.md` "Open Questions") to the work it blocks. Acceptance evidence: each feature's plan records how its question was answered, or why the work it blocks is out of that feature's scope:
  - Questions 1 and 2 (the thread contract, the buckets and kinds): answered at task 1.2, or they block feature 1.
  - Questions 3 and 11 (the service-category field, the accountant on the roster): block accounting go-live, not building. Feature 1 tests the refusal.
  - Questions 4, 5 and 9 (manual-path claims, the escape threshold, the participation escape): block feature 3.
  - Questions 6 and 7 (automatic posting, the patient's share and denials): block feature 4.
  - Questions 8, 13, 14, 15 and 17 (Good Faith Estimate parameters, CPT licensing, the discount for federal-program beneficiaries, statement rules): block production use of feature 2's estimates, CPT-coded items, discount and statements. Building and synthetic acceptance are not blocked.
  - Question 10 (write-offs before the material amount is set): feature 2 builds the tier-4 rule either way.
  - Question 12 (the old-system import): assigned to feature 4 unless a practice needs it sooner.
  - Questions 16 and 18 (credit-balance clocks, retention periods): block production use of feature 4's credit-balance cases, and the retention profiles of features 2 and 4.
  - Question 19 (the clearinghouse agreement) and 25 (Medicare particulars): block feature 5.
  - Questions 20 to 24 (the accountant's approvals): block accounting go-live, the first estimator version, and the material amount. Feature 2's handling of prepayments (question 24) is built as PY-03 states and reclassified only by a new version.
  - Questions 26 and 29 (filing windows, enrollment backdating): block feature 3's production use of the filing clock and the escape.
  - Questions 27, 28 and 30 (payer-level adjustments, secondary claims, paper remittances): block feature 4. Question 28 also informs feature 3's use of the manual path for secondary claims.

## 4. Acceptance and Closure

- [ ] 4.1 Complete implementation and verification only through the features in section 2. Acceptance evidence: `closure-evidence.md` maps every requirement and scenario to accepted evidence.
- [ ] 4.2 Land each feature's leg pull requests and the assembly root pin bump. Acceptance evidence: pull-request identifiers, exact heads, check results and review dispositions are recorded.
- [ ] 4.3 Run final strict validation on the final revision. Acceptance evidence: the command, version, timestamp, revision and output are recorded, with exit code 0.
- [ ] 4.4 Promote nothing to canonical specs before the features land (ruling 109). Archive this change only after 4.1 to 4.3 are complete for features 1 to 6, and move the thread contract's canonical schema to the spec repository's `contracts/` directory with its digest unchanged. Acceptance evidence: the archive date and path, the schema move with its matching digest, and the delta-spec sync disposition are recorded.
