## Context

openPractice is an assembly root whose code leg is empty; the Frappe application `open_practice` does not exist yet. Constitution v1.0.0 (`.specify/memory/constitution.md` in `opensoft/openPractice`, ratified 2026-08-24) is in force: I (HIPAA by design), II (security review), III (test-first), IV (clinical boundary), V (versioned contracts) and VI (portability). The framing decisions FD-1 to FD-11 were ratified on 2026-08-27. The ones that shape this change most are FD-2 (learned expectation, contract as arbiter), FD-3 (always-closed books), FD-4 (the self-pay and Good Faith Estimate money path), FD-5 (four tiers and deadline escapes), FD-7 (one thread, contract first), FD-8 (live remittance and settlement as beta acceptance), FD-9 (old-system balances) and FD-11 (licensed overlay).

Five OpenSpec changes are merged on `main` at `0a82fc2`, all `Status: draft`. They specify the perimeter of the money path and depend on its middle, which no change specifies. The written critique of 2026-11-15 called this "The Missing Middle". The product owner, Brett Heap, ruled that the revenue-cycle core is written next and perimeter work is paused (ruling 110), then answered decisions 112 to 132 on its scope. This change encodes them. Everything those rulings do not state is a design call marked PROPOSED (`CR-n`, listed under "Design calls for owner review"), and is kept fail-closed where it touches money leaving the practice, a claim going out, or a patient being told an amount. **Rulings are not ratification.** This change stays `Status: draft`, and `owner-ratification.md` stays PENDING until the owner completes it; under rulings 109 and 110 nothing is handed to build before that.

Requirement IDs prefix every requirement name in the fifteen spec deltas: `TH` (`encounter-thread`), `AP` (`administrative-appointment`), `SC` (`service-catalog-and-pricing`), `CG` (`charge-capture`), `SL` (`money-subledger`), `RT` (`reason-taxonomy`), `RE` (`revenue-estimate`), `GF` (`good-faith-estimate`), `PS` (`patient-balance-and-statements`), `PY` (`patient-payments-and-refunds`), `EV` (`coverage-eligibility-evidence`), `EG` (`participation-gate`), `CL` (`claim-lifecycle`), `CP` (`claim-pathway-adapter`) and `RM` (`remittance-posting`). Requirement IDs of other changes are cited as those changes define them; a requirement of `add-reimbursement-retention-control` or `define-customer-financial-data-boundaries`, which carry no IDs, is cited by its name. Requirements that exist only on an open pull request are cited by ID and described, never quoted. Requirement text names them by ID alone, with no pull-request number, so it stays true once they merge; "Dependencies" below says which pull request brings each.

### Sources

- The five merged changes on `main` at `0a82fc2`, read in full for the requirements this change touches.
- Draft PR opensoft/openPractice-spec#7 (branch `change/follow-up-ruling-93-contract-gaps`), read at head `e746f58`: EB-13 and EB-15 as amended, and the follow-up calls F-1 to F-5.
- Draft PR opensoft/openPractice-spec#8 (branch `change/critique-2026-11-15-corrections`, stacked on PR #7), first read at head `4bce39a` and re-checked at heads `050bdfd`, `f96ee1b` and `f3bed0a`: PR-15 and design call F-7, BAB-18 and design calls F-8 to F-10, AM-01, AO-04 and AO-07, and the accounting schema as it amends it. Between those heads BAB-18 gained a default order for a split and a rule that splits use exact arithmetic, and F-7 was reworded without changing its alternative. At `f96ee1b` PR-15 no longer merges parties: it records two parties as the same person with a reversible link that moves nothing, and holds a contact when a confidential-communication restriction leaves no permitted channel. At `f3bed0a` the links form same-person groups whose restrictions apply across the group without ever routing to another party's alternate channel, a fact for a service date on which another record of the group holds a charge is never charged automatically, and a staying record's charge is held until a duplicate's is voided or credited. TH-06, TH-09, CG-03, CG-05, GF-07 and PS-06 follow that text. The accounting schema is unchanged across those heads. The branch may still move.
- Draft PR opensoft/openPractice-spec#10 (branch `change/define-practice-readiness`), first read at head `e9748d7` and re-checked at head `d4724e1`: SR-01 to SR-04, design call RD-11, and its findings I-1 and RI-51 that the roster has no actor type or recipe for the practice's accountant. At `d4724e1`, SR-04 also requires the accountant's own attestation and a licence check before an accountant's sign-off counts, and RI-51 is pending there as owner decision 141, with decisions 139 and 140 on who may give the accountant's sign-off.
- Draft PR opensoft/openPractice-spec#9 (branch `docs/framing-decisions-v2`), read at head `ebc8457`, for its index of owner rulings only. It is navigation, not normative.
- [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md): FD-1 to FD-11 and the 2026-11-14 amendments.
- [`ideation/brainstorm/openpractice-domain-framework.md`](../../../ideation/brainstorm/openpractice-domain-framework.md): the chain of objects (§4), receivable option A (§5.2), the posting events (§5.4), revenue recognition (§5A), the Claim Pathway Adapter (§8) and the include/defer matrix (§9). Proposal status; rulings 28 to 33 adopt parts of it.
- The small-practice beta brainstorms (all `Status: brainstorm`): [visit thread](../../../ideation/brainstorm/openpractice-small-practice-beta-visit-thread.md), [self-pay and GFE](../../../ideation/brainstorm/openpractice-small-practice-beta-self-pay-and-gfe.md), [enrollment-gated billing](../../../ideation/brainstorm/openpractice-small-practice-beta-enrollment-gated-billing.md), [scope and sequencing](../../../ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md), [feature normalization](../../../ideation/brainstorm/openpractice-small-practice-beta-feature-normalization.md), [day rhythm](../../../ideation/brainstorm/openpractice-small-practice-beta-day-rhythm.md) and [day-one migration](../../../ideation/brainstorm/openpractice-small-practice-beta-day-one-migration-and-activation.md); and the feat reviews for [revenue cycle](../../../ideation/review/openpractice-feat-review-revenue-cycle.md), [revenue integrity](../../../ideation/review/openpractice-feat-review-revenue-integrity-and-product-shape.md) and the [synthesis](../../../ideation/review/openpractice-feat-review-synthesis.md). This change adopts their thread vocabulary, transition table and non-visit case types as design calls, not as ratified text.
- The critique of 2026-11-15 itself. PR #8 adds it to the repository under ruling 111; it is not on `main`, so it is not linked.

## Owner rulings

Rows 112 to 132 have the source **Brett Heap, 2026-11-15**, who answered decisions 112 to 132 with the words "for the rest, go with your recomendations". Each row quotes the question as it was put to him and the recommendation he accepted. For ruling 132 the accepted words are a sentence and three bullets, given here as text. The note sent under the table about decision 113 is not part of the ruling.

| # | Ruling | Encoded in |
|---|---|---|
| 112 | (2026-11-15) Question: "One change or several?" Accepted: "One change, with its own ordered multi-feature hand-off, as you allowed in ruling 1." | `proposal.md` "The six features, in order"; `tasks.md` §1.2 and §2; T-1 |
| 113 | (2026-11-15) Question: "Which payers in the MVP?" Accepted: "Self-pay first, then Medicare Part B as primary. Commercial and secondary claims later; the data shape allows both from the start." | CL-02, CP-06; design call CR-46 |
| 114 | (2026-11-15) Question: "Which slice is built first?" Accepted: "The self-pay visit. It fixes every data shape and needs no payer, clearinghouse or processor." | `tasks.md` §2.2 (feature 2 is the first buildable slice) |
| 115 | (2026-11-15) Question: "Does openPractice book appointments in the MVP?" Accepted: "Yes, minimally: book, reschedule, cancel, no-show and walk-in. No reminders, waitlist or self-scheduling." | AP-01 to AP-09 |
| 116 | (2026-11-15) Question: "Who supplies billing codes? (framework D6)" Accepted: "The EHR supplies coded facts and a named human biller confirms them. Staff enter codes when there are none. openPractice never infers codes." | CG-02, CG-04; T-6 |
| 117 | (2026-11-15) Question: "Deployment shape? (framework D4)" Accepted: "One Frappe app with a module per area. Adapters are separate packages." | CP-08 |
| 118 | (2026-11-15) Question: "How are claims submitted at first?" Accepted: "Through a simulated payer, plus a manual path that records a claim sent outside the system. The live clearinghouse is a later feature." | CP-03, CP-04, CP-05 |
| 119 | (2026-11-15) Question: "Do claim batches need an explicit release?" Accepted: "Yes, by a named signer, keeping the escape near a filing deadline." | CL-05, CL-06; T-4 |
| 120 | (2026-11-15) Question: "May the system post exactly matched remittance lines by itself?" Accepted: "Yes: post and notify, undoable. Anything not exact is a proposal for a human." | RM-04, RM-05; T-3 |
| 121 | (2026-11-15) Question: "How much Good Faith Estimate?" Accepted: "Single-provider estimates for scheduled self-pay care: issue, replace, deliver with proof, retain, watch the variance, handle a dispute." | GF-01 to GF-12 |
| 122 | (2026-11-15) Question: "How much of statements and payments?" Accepted: "Record desk payments, receipts, allocations and credits. Refunds stay human-only. Printable statements. Processor and pay link later." | PY-01 to PY-04, PY-07, PY-10; PS-04 |
| 123 | (2026-11-15) Question: "How much of the revenue estimator?" Accepted: "The full estimate record now, with a simple first method your accountant approves before accounting goes live." | RE-01 to RE-03, RE-09 |
| 124 | (2026-11-15) Question: "How is the money ledger shaped?" Accepted: "Patient balances belong to the guarantor account. Every entry moves money between two fixed buckets, so it always balances." | SL-01, SL-02, SL-06; PS-01; T-2 |
| 125 | (2026-11-15) Question: "How are reason codes organized?" Accepted: "A fixed list we ship, on two axes: kind of money movement and service category. Add a service-category field to the accounting contract if the gap is confirmed." | RT-01 to RT-04; the gap is confirmed and PA-1 proposes the field |
| 126 | (2026-11-15) Question: "Where do prices and payer expectations come from?" Accepted: "One practice price list and one self-pay discount. Payer expectations come from the estimator, not loaded fee schedules." | SC-03, SC-04, SC-07; RE-03; T-5 |
| 127 | (2026-11-15) Question: "Eligibility and prior authorization?" Accepted: "Recorded by hand as evidence. Electronic checks arrive with the clearinghouse." | EV-01 to EV-03 |
| 128 | (2026-11-15) Question: "Hold claims when a provider is not enrolled with the payer?" Accepted: "Yes, minimally: hold, never reject, and release in one action once enrolled." | EG-03, EG-04; design calls CR-44 and CR-45 |
| 129 | (2026-11-15) Question: "What makes a write-off "material"?" Accepted: "Each practice sets an amount at setup, confirmed by its accountant. Below it is do-and-notify; everything else is human-only." | PS-11, RE-09 |
| 130 | (2026-11-15) Question: "Cash drawer and deposit slip?" Accepted: "Deposit slip in the self-pay slice. Drawer count and close sheet in a later change." | PY-05; `proposal.md` non-goals |
| 131 | (2026-11-15) Question: "Balances carried over from an old system?" Accepted: "Define the record type now. Build the import before the first practice that needs it." | TH-08, SL-11; `tasks.md` §2.4 |
| 132 | (2026-11-15) Question: "What may agents do?" Accepted: "Everything starts at "propose", per ruling 37. A practice may opt up as far as: **Act with notice:** booking, rescheduling, drafting charges, allocating payments, building claims. **Act after a delay:** sending statements. **Propose only:** cancelling, finalizing charges, issuing estimates, releasing claims, write-offs, refunds, estimator versions." | TH-13, and each capability's action types |

The earlier rulings below govern this change. Rows recorded on `main` quote the governing sentence of the table row in the change named. Rows 87, 89, 100, 103, 107, 109 and 110 are recorded only in tables of open pull requests; their accepted words are the owner's own and are quoted from those tables at the heads read, as the readiness draft does.

| # | Ruling (governing sentence, or accepted words) | Governs here |
|---|---|---|
| 1 | "Handoff shape **A**: an ordered multi-feature exception. Pre-stated as the owner's ruling; the field in `owner-ratification.md` is left for him to confirm." (`define-mvp-practice-foundation`) | The precedent ruling 112 names; `tasks.md` §2 |
| 4 | "Dates: line date of service; GL date = date of service if that period is open, otherwise the first open date with a late-charge flag (FD-3); invoice date = finalization date; due date from statement terms; sync timestamp kept separately and never an accounting date; each adapter verifies line service dates and cash-basis support before go-live." (`define-mvp-practice-foundation`) | CG-09, SL-03, PS-05 |
| 20 | "Coverages are zero or more; self-pay patients have none." (`define-mvp-practice-foundation`) | CG-09, GF-02, CL-02 |
| 21 | "Manual charge entry is allowed for a linked patient while that EHR reports no billable-facts capability. It is marked manual, with provenance." (`define-mvp-practice-foundation`) | CG-02 |
| 24 | "Membership agreements, subscription renewals, retail sales and other non-medical commerce invoicing are out of scope." (`define-mvp-practice-foundation`) | SC-06; `proposal.md` non-goals |
| 26 | "This change owns the posting mechanics of `openpractice-accounting-integration/1.0`: posting identity and idempotency, batches and their freeze, release, retries, the reconciliation report, the posting modes and the BAA gate." (`define-mvp-practice-foundation`) | SL-01, RT-04; `proposal.md` non-goals |
| 28 | "The MVP default and recommended setup is `summary-journal`, the framework document's Option A: summarized, balanced postings to payer-class control accounts, with patient-level receivable detail kept in openPractice." (`define-mvp-practice-foundation`) | SL-03, SL-09, RT-04 |
| 30 | "Net revenue is the constrained expected entitlement, estimated at the date of service and re-estimated at each close; the gross charge is a memo or gross line carried with its deduction." and "D8: the practice's accountant approves the reporting basis, estimator, classification, materiality and reopening policy before go-live." (`define-mvp-practice-foundation`) | RE-01 to RE-09, RT-07 |
| 31 | "openPractice is the system of record for the administrative appointment; the EHR owns the encounter and keeps an appointment reference if needed." (`define-mvp-practice-foundation`) | AP-01, AP-09 |
| 32 | "One Party with roles. "Customer" is a party role only for membership and retail buyers." (`define-mvp-practice-foundation`) | SL-06 |
| 33 | "Automation stays propose-only. Each action's FD-5 tier decides whether it needs an approval, a notification or a delay-and-cancel release, including the deadline escapes." and "Tier 4 stays human-only: hard delete, enabling `customer-detail`, refunds leaving the practice, material adjustments and record merges." (`define-mvp-practice-foundation`) | TH-13, PS-11, PY-07; T-3 |
| 37 | "Every action type starts at propose, and the practice opts each one up. Tier 4 stays human-only." (`define-actors-agents-and-work-inbox`) | TH-13 |
| 41 | "A payment hold blocks non-urgent clinical messaging and new non-urgent scheduling **only**." (`define-patient-gateway-and-escalation`) | AP-07; T-8 |
| 46 | "The read-only answer types are in the MVP agent operations contract (1.0), alongside the registry operations." (`define-actors-agents-and-work-inbox`) | AP-09, PS-09 |
| 87 | (2026-11-15; recorded in PR #7) "Only confirmed ones. If a published diagnosis is later refuted, a retraction is sent." | CG-02 |
| 89 | (2026-11-15; recorded in PR #7) "Add a retraction reason code to the contract now, while 1.0 is still unratified. That is a small follow-up PR in openPractice-spec …" | CG-05, CG-08 (EB-15 as PR #7 amends it) |
| 100 | (2026-11-15; recorded in PR #8) "Not in version 1. Lower the rung in openPractice instead." | TH-13 |
| 103 | (2026-11-15; recorded in PR #8) "Decimal text plus a currency code on the wire, as the accounting contract already does. Whole cents in storage." | SL-04, RE-03, SC-04; the thread schema's `money` |
| 107 | (2026-11-15; recorded in PR #8 and PR #10) "Allowed, with each sign-off recording the role used. A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change." | CG-04, CL-05, PS-11, PY-08, RE-02, GF-03; T-16 |
| 109 | (2026-11-15; recorded in PR #10) "No. Ratify first, which is yours to do, then archive each change after its feature lands." | `tasks.md` §1 and §4 |
| 110 | (2026-11-15; recorded in PR #10) "Yes. Its scope and decisions will follow as their own list." | This change; rulings 112 to 132 are that list |

## Defined terms

| Term | Meaning in this change |
|---|---|
| Thread | The one record that follows a visit the practice means to happen, or one non-visit case, from start to finish (TH-01). It indexes the charges, claims, payments and documents that belong to it |
| Station, condition | Where a thread is on the path (one of ten), and why it is not moving (`held`, `stuck` or `disputed`) (TH-04, TH-05) |
| Bucket | One of eighteen fixed balances openPractice keeps, such as patient receivable or cash clearing (SL-02). Buckets are not general-ledger accounts; each destination maps them to its own accounts |
| Ledger entry | One movement of money between two buckets, with a reason code (SL-01) |
| Reason code | A kind of money movement, plus, for revenue entries, a service category (RT-01) |
| Guarantor account | The account of the person who owes a patient's balance (PR-06); patient balances belong to it (SL-06) |
| Charge set, charge line | All the billable services of one visit, and one of them, priced and payer-agnostic (CG-01) |
| Final charge | A charge set a named human biller has confirmed (CG-04) |
| Estimate | What the practice expects to be entitled to for a charge line, reduced so a large later reversal is unlikely (RE-01) |
| Claim, claim version, batch | A request to one payer for one visit; each version as submitted; and the group a named signer releases (CL-01, CL-05, CL-07) |
| Remittance | The payer's answer, which is not the money itself (RM-01) |
| Good Faith Estimate | The written estimate a self-pay patient is given for scheduled care (GF-01) |
| Statement, receipt, deposit slip | Frozen documents that tell a guarantor what is owed, confirm a payment, and list desk money taken to the bank (PS-03, PY-02, PY-05) |
| Simulated payer, manual path | A test adapter that behaves like a payer, and a record of a claim sent outside openPractice (CP-03, CP-04) |

## Goals / Non-Goals

**Goals**

- Specify the money path from a booked visit to a settled balance precisely enough that each of the six features can be built test-first.
- Fix the data shapes the critique worried about before any of them is built: the thread, the ledger, the reason codes, the charge, the claim, the remittance and the balance.
- Satisfy the prerequisites that `define-mvp-practice-foundation` `tasks.md` 2.3 and 2.4 wait on, and give PH-01, PH-02 to PH-09 and AO-11 the records they assume.
- Keep every step that tells a patient an amount, sends a claim, or moves money out of the practice behind a named human or a fixed rule, fail-closed.

**Non-Goals**

- Clinical content, the books, posting mechanics, deposit explanation, memberships and retail (`proposal.md` "Non-goals").
- Any legal, accounting or billing rule. Where one matters, this change names who decides it and fails closed until they do.
- Editing another change. Every amendment below is proposed for its owner.

## Ownership matrix

| Object or function | This change | Another change | Outside system |
|---|---|---|---|
| Visit thread and non-visit cases | **Source** (TH) | Work items are shown in WI-01 (`define-actors-agents-and-work-inbox`) | — |
| Administrative appointment | **Source** (AP, ruling 31) | Payment-hold clause PH-01 | The EHR may keep a reference |
| Encounter and clinical facts | References only (TH-14) | EB-13 to EB-18 (`define-mvp-practice-foundation`) | **Source**: the EHR |
| Catalog, prices, self-pay discount | **Source** (SC) | Offering-route rules (`define-customer-financial-data-boundaries`) | — |
| Charges, estimates, claims, remittances, balances, statements, payments, refunds | **Source** | Receivable ownership BAB-01 | — |
| Ledger buckets and reason codes | **Source** (SL, RT) | Account mapping per destination BAB-06; posting AI-01 to AI-23 | Accounts in the accounting system |
| Deposit explanation and bank register | Supplies payments, trace numbers and deposit slips | **Owner**: BAB-09 to BAB-14, AI-17 | Bank lines from the accounting system |
| Patient, guarantor, coverage, party | References only | **Source**: PR-01 to PR-15 | — |
| Retention, authorization and lifecycle evidence of claims and remittances | Calls them | **Owner**: `add-reimbursement-retention-control` | — |
| Claim submission, remittance, eligibility transport | Adapter interface (CP) | — | Clearinghouse and payers |

## Decisions

### 1. One change, six features, and how the foundation's features fit (ruling 112)

The workflow protocol hands one change to exactly one Speckit feature. Ruling 112 grants this change the same ordered multi-feature exception ruling 1 granted the foundation (T-1). The six features and their gates are in `tasks.md` §2. The foundation's four features slot in as follows:

| Order | Feature | Change |
|---|---|---|
| 1 | Patient registry (Slice 0) | `define-mvp-practice-foundation` Feature 1 |
| 2 | **Foundations:** thread, ledger, reason codes, estimate record | This change, feature 1 |
| 3 | EHR patient link (Slice 0), in parallel with the above | `define-mvp-practice-foundation` Feature 2 |
| 4 | **Self-pay visit, end to end**, building inside itself the narrow pieces of BAB-16 and BAB-17 it needs (CR-78, PA-3) | This change, feature 2 |
| 5 | Accounting contract and ERPNext adapter (Slice 4), after this change's feature 1; it goes live only once PA-1 is adopted | `define-mvp-practice-foundation` Feature 3 |
| 6 | **Claims against a simulated payer**, with the foundation's clinical-fact consumption folded in | This change, feature 3, absorbing `define-mvp-practice-foundation` Feature 4 (its task 2.4 allows it) |
| 7 | **Remittance posting and full balances** | This change, feature 4 |
| 8 | **Live clearinghouse and Medicare adapter** | This change, feature 5 |
| 9 | **Payment processor and pay link** | This change, feature 6 |

The registry comes first because the ledger's counterparty accounts reference the guarantor account and coverage versions it defines (SL-06), and PR-15's one-party rule settles the identity the critique worried about. The registry refers to the core's records only by reference (PR-11, PR-12), so it does not wait for the whole core.

### 2. The thread is an index with a contract, and money lives in the ledger

The thread follows FD-7 and the visit-thread brainstorm: minted at intent, ten stations, three conditions, a derived clock and amount, an owner, links, a re-association key set and enumerated cases (TH-01 to TH-12). It never stores a balance (TH-03, SL-08). It is defined by `openpractice-thread/1.0`, whose canonical schema is `contracts/openpractice-thread-1.0.schema.json`, SHA-256 `c861dd82841c1c3f38826868684e9b0ce927fced5c03b623b40aecfba4f2185b` (TH-11). The schema covers the thread snapshot and the transition record; the legality of a move is TH-04's table.

### 3. The ledger: two buckets per entry, eighteen buckets, one reason code (rulings 124 and 125)

Each entry debits one bucket and credits another by one amount (SL-01), so the ledger always balances. The reason code's kind fixes the pair and the neutral label (RT-02); the service category rides on revenue entries (RT-03). The buckets are openPractice's balances, not accounts, so BAB-02's bar on a general ledger holds (T-2). Every balance, including the thread's amount and a statement's total, is read from entries (SL-08).

**Worked example (synthetic).** It uses the framework's §5A.3 numbers. An insured office visit is priced 200.00, the approved estimator expects 114.00, and the payer allows 120.00, pays 96.00 and assigns 24.00 to the patient.

| Event | Kind | Debit | Credit | Amount |
|---|---|---|---|---|
| Charge finalized (CG-09) | `service_insured` | `unbilled_receivable` | `gross_service_revenue` | 200.00 |
| Estimate at the date of service (RE-01) | `gross_to_net_estimate` | `revenue_deduction` | `gross_to_net_allowance` | 86.00 |
| Claim accepted (CL-12) | `claim_accepted` | `payer_receivable` | `unbilled_receivable` | 200.00 |
| Remittance (RM-07) | `contractual_adjustment` | `gross_to_net_allowance` | `payer_receivable` | 80.00 |
| | `payer_payment` | `cash_clearing` | `payer_receivable` | 96.00 |
| | `patient_share_transfer` | `patient_receivable` | `payer_receivable` | 24.00 |
| | `estimate_increase` | `gross_to_net_allowance` | `revenue_deduction` | 6.00 |
| Patient pays at the desk (PY-01) | `patient_payment` | `cash_clearing` | `patient_receivable` | 24.00 |
| Deposits explained (BAB-14) | `deposit_explained` | `deposited` | `cash_clearing` | 120.00 |

Every receivable and the allowance end at zero, `gross_service_revenue` less `revenue_deduction` is 120.00 (200.00 less 86.00 plus 6.00), and the money deposited is 120.00. A self-pay visit at the same price with a 30 percent discount, paid at checkout, posts `service_self_pay` 200.00, `self_pay_discount` 60.00 and `patient_payment` 140.00. Its estimate posts no further deduction: the `self_pay_discount` entry already brings net revenue to 140.00, and under the shipped starting value a charge paid in full carries no collectibility constraint (RE-01, RE-03; CR-17).

**Worked example for the accountant: a write-off after a partial payment (synthetic; RT-09, CR-61).** A self-pay procedure is priced 200.00 with a 60.00 discount, and the approved version holds back a 20.00 collectibility constraint. The patient pays 100.00 and the practice writes off the other 40.00 as a price concession.

| Event | Kind | Debit | Credit | Amount |
|---|---|---|---|---|
| Charge finalized (CG-09) | `service_self_pay` | `patient_receivable` | `gross_service_revenue` | 200.00 |
| | `self_pay_discount` | `revenue_deduction` | `patient_receivable` | 60.00 |
| Estimate (RE-01) | `gross_to_net_estimate` | `revenue_deduction` | `gross_to_net_allowance` | 20.00 |
| Patient pays (PY-01) | `patient_payment` | `cash_clearing` | `patient_receivable` | 100.00 |
| Write-off, up to the remaining allowance (RT-09) | `patient_write_off_against_allowance` | `gross_to_net_allowance` | `patient_receivable` | 20.00 |
| Write-off, the excess | `price_concession_write_off` | `revenue_deduction` | `patient_receivable` | 20.00 |

The receivable and the allowance end at zero, deductions total 100.00 (60.00 + 20.00 + 20.00), and net revenue is 100.00, equal to the cash collected. No `estimate_decrease` posts for the write-off (RE-05). Had the whole 40.00 gone to `revenue_deduction`, and a true-up then lowered the estimate for it as well, net revenue would have fallen to 80.00 and then 60.00 against 100.00 collected. That is the double count RT-09 prevents.

### 4. Claims: Medicare Part B first, a simulated payer and a manual path (rulings 113, 118, 119)

A claim is one request to one payer for one visit, in coverage order (CL-01). Feature 3 builds Medicare Part B primary claims against the simulated payer. Every other claim, commercial or secondary, is recorded through the manual path until a later feature builds it, so the receivable stays true (CR-46). Batches leave only by a named signer's release (CL-05), with the filing-deadline escape (CL-06). Submission goes through the retention change's authorization and idempotent outbox (CL-11). The receivable moves from unbilled to billed when a claim is accepted, not when it is sent (CR-15).

### 5. Self-pay, the Good Faith Estimate, statements and payments (rulings 114, 121, 122, 130)

The self-pay visit is the first buildable slice. It exercises booking, the catalog and price list, the self-pay discount, manual charges, the estimate record, the Good Faith Estimate, desk payments, receipts, the deposit slip and a printable statement, with no payer, clearinghouse or processor. The Good Faith Estimate's legal parameters live in a counsel-verified parameter set, and openPractice issues none until a version is verified (GF-03, CR-30).

### 6. The first estimator (ruling 123)

Every final charge line gets an estimate record now (RE-01). The first method multiplies the gross by an accountant-set ratio per payer class and service category, less an accountant-set constraint. Self-pay is price less discount less a collectibility constraint. For a charge paid in full, the constraint is whatever the approved version states, with a shipped starting value of zero (RE-03, CR-17). Nothing approves an estimator version except the practice's accountant, and the roster cannot yet hold one (`define-practice-readiness` I-1, RI-51). Until that is settled, estimates wait and accounting cannot go live (RE-02, T-16).

### 7. Deployment (ruling 117)

One Frappe app, `open_practice`, with a module per area, and every adapter as a separate package that the core never imports (CP-08). This extends AI-04's rule from the accounting adapter to claim and payment adapters.

## Governance tiers (FD-5) for actions this change adds

The tier column states only what a requirement states, and names the design call where the tier is PROPOSED rather than ruled. Where no requirement sets a tier, the column says so: each feature's plan proposes one, and agents stay at `propose` meanwhile (TH-13).

| Action | Tier (requirement) | Signer | Agent ceiling (ruling 132, TH-13) |
|---|---|---|---|
| Book or reschedule an appointment | 1 (AP-02, AP-03; CR-82, CR-83) | No sign-off | `act_with_notice` |
| Cancel an appointment | 2 (AP-04; CR-84) | No sign-off; notification | `propose` |
| Record a no-show or a patient who left unseen | Not ruled; 2 until ruled (AP-04; CR-88) | No sign-off; notification | `propose` |
| Close a visit at `seen` with nothing to bill | Not ruled; 2 until ruled (TH-04; CR-88) | Named human; notification | `propose` |
| Undo a thread's minting | Not ruled; 2 until ruled (TH-04, TH-07; CR-88) | Named human; notification | `propose` |
| End a hold by closing the work it guards | 2 (TH-06; CR-90) | Named human, with a reason; notification | `propose` |
| Decide whether a later payer is billed first, which posts `payer_share_transfer` or `patient_share_transfer` | Not ruled; 2 until ruled (RM-07; CR-54, CR-88, CR-91) | Named human; notification | `propose` |
| Move a payment recorded against the wrong guarantor account | 2 (SL-05; CR-99) | A person; notification | `propose` |
| Record a fee without a matched processor or bank record | 2 (SL-14; CR-94) | A person, citing evidence; notification | `propose` |
| Record an eligibility check | 1 (EV-01; CR-85) | No sign-off | `propose` |
| Record an authorization | Not ruled; 2 until ruled (EV-02; CR-88) | No sign-off; notification | `propose` |
| Release a claim held for authorization at the filing threshold | Not ruled; 2 until ruled, with a recorded reason (EV-02; CR-74, CR-88) | Named human | `propose` |
| Record a participation record, which can release held claims | Not ruled; 2 until ruled (EG-01; CR-88) | No sign-off; notification | `propose` |
| Change the catalog, a price, the discount or a category mapping | 2 (SC-08; CR-86) | No sign-off; notification | `propose` |
| Draft a charge set | None needed; a draft posts nothing (CG-03) | No sign-off | `act_with_notice` |
| Finalize a charge set | Sign-off (CG-04; T-6) | Named human biller | `propose` |
| Issue or replace a Good Faith Estimate | 2 (GF-12) | Named human only | `propose` |
| Record a payment, with its receipt and allocation | 1 (PY-01, PY-02, PY-04) | The person who took it | `act_with_notice` for allocation; `propose` otherwise |
| Reallocate a payment (within one guarantor account) | 2 (SL-07; CR-65, CR-66) | A person; notification | `propose` |
| Move a prepayment that will not be applied to unapplied credit | Posted with the disposition or finalization that causes it, under that act's tier (PY-03, AP-04; CR-56, CR-69) | As that act | As that act |
| Transfer a balance between guarantor accounts | 2 at least (PS-12; CR-57) | Named human only | `propose` |
| Record a chargeback or a returned item | 2 (PY-09; CR-66) | A person; notification | `propose` |
| Identify the owner of an unidentified receipt | 2 (PY-06; CR-66) | A person; notification | `propose` |
| Move a patient credit to refund liability | 2 (PY-07; CR-66) | A person; notification | `propose` |
| Build a claim | None needed; building sends nothing (CL-03, CL-05) | No sign-off | `act_with_notice` |
| Release a claim batch | Sign-off (CL-05; T-4) | Named human | `propose` |
| Deadline escape of an unreleased claim | 3 (CL-06) | No sign-off; notification and cancel window | Not an agent action |
| Release held claims after enrollment | 2 (EG-04, TH-06; CR-87) | No sign-off; notification | Not an agent action |
| Record a claim sent outside the system, and its acceptance | Not ruled; 2 until ruled (CP-04; CR-77, CR-88) | Named human | `propose` |
| Snooze a stuck thread | Not ruled; 1 until ruled (TH-12; CR-88) | No sign-off | `propose` |
| Post an exactly matched remittance | 2 (RM-04) | Shipped rule; notification; undoable | Not an agent action |
| Post a remittance that is not exactly matched | Approval of a proposal (RM-05) | Named human | `propose` |
| Undo an automatic remittance posting | 2 (RM-04; CR-53, CR-66) | A person; notification | `propose` |
| Classify an adjustment code the table does not cover | 2 (RT-05; CR-66) | A person; notification | `propose` |
| Identify a payer overpayment | 2 (RM-09; CR-66) | A person; notification | `propose` |
| Appeal a recoupment | 2 (RM-09; CR-66) | A person; notification | `propose` |
| Agree to a recoupment, letting money leave by offset | 4 (RM-09; CR-66) | Named human only | `propose` |
| Print a statement and record it as issued | 1 (PS-04; CR-66) | A person | `propose` |
| Set or change statement terms | 2 (PS-05; CR-66) | A person; notification | `propose` |
| Import a batch of old-system balances | Sign-off (SL-11; CR-66) | Named human, after reviewing count and memo total | `propose` |
| Send statements from openPractice | 3 (PS-10) | No sign-off; cancel window | `act_after_delay` |
| Write-off whose 30-day total on the guarantor account is below the material amount | 2 (PS-11; CR-64) | No sign-off; notification | `propose` |
| Write-off whose 30-day total is at or above the material amount, or any write-off before one is set | 4 (PS-11; CR-39, CR-64) | Named human only | `propose` |
| Refund leaving the practice | 4 (PY-07) | Named human only | `propose` |
| Record that a signed refund was issued outside openPractice | Not ruled; 2 until ruled (PY-07; CR-88) | A person; notification | `propose` |
| Approve an estimator version or the policy record, including the credit-loss method | Approval (RE-02, RE-09, RT-10; D8) | The practice's accountant only, never self-approved | `propose` (a draft only) |
| Record counsel's verification of a Good Faith Estimate parameter set | Approval (GF-03) | An authorized person naming counsel, never self-approved | Not an agent action |
| Re-estimate at close | Automatic (RE-06; FD-3) | No sign-off | Not an agent action |

**Deadline escapes (FD-5).** Every hold names its escape in the requirement that places it (TH-06, TH-12). A ready, unreleased claim escapes at the filing threshold, and CL-06 defines ready (CR-73). A participation hold escapes only while an application is pending (EG-06). An offering-route hold escapes as BAB-16 states, and an identity-review hold as EB-18 states. A hold for a retracted fact never escapes into a claim; it escalates (CL-06). A documentation hold and an authorization hold escalate and never release by themselves, though a named human may release an authorization hold with a recorded reason (CG-07, EV-02; T-20). Thread holds never stop care (TH-06, EV-04).

## Constitution Check (pre-handoff, against v1.0.0)

| Principle | Obligation | Status |
|---|---|---|
| I. HIPAA by design | Every record here is PHI or money behind access controls. Reads of patient-tied ledger records are audited (SL-12). No PHI in logs, keys or diagnostics (TH-15). Card and bank numbers are never stored (PY-01, RM-01). The clearinghouse activates only with a verified agreement (CP-05). | Requirement level; a safeguard plan is owed per feature |
| II. Security review | Every feature touches PHI and money. Claim release and refunds need a named human; nothing autonomous is on by default (TH-13). | Owed per feature |
| III. Test-first | Contract tests for the thread schema and each adapter contract, calculation tests for the ledger, rounding and estimates, synthetic fixtures only (TH-16, CP-09). | Owed per feature |
| IV. Clinical boundary | References only; codes come from the EHR or from staff and are never inferred (TH-14, CG-02). | Satisfied |
| V. Versioned contracts | `openpractice-thread/1.0` has one canonical schema and digest. The claim-pathway, remittance and processor contracts are defined before the features that need them (CR-24). | Satisfied for this change |
| VI. Portability | One Frappe app; adapters optional; no dependency on ERPNext's record types (CP-08). | Satisfied |

## Design calls for owner review

Every call below goes beyond rulings 112 to 132. Each is PROPOSED, and each is also an open question for the owner; ratification records his disposition of each.

| # | Call | Where |
|---|---|---|
| CR-1 | **PROPOSED.** One thread per visit-intent, minted only by a booking, a walk-in, or facts or a manual charge for a visit with no thread. Waitlist entries and inbound pages mint nothing in this change | TH-01, TH-02 |
| CR-2 | **PROPOSED.** The thread is a contract with a canonical schema and digest, `openpractice-thread/1.0`, like the integration contracts, so FD-7's "contract before any consumer" is checkable | TH-11, `contracts/` |
| CR-3 | **PROPOSED.** The brainstorm's ten stations, three conditions and transition table, with `banked` meaning the practice recorded the money, not that it reached the bank, and one added move for late coverage | TH-04, TH-05 |
| CR-4 | **PROPOSED.** The closed list of six hold reasons, each placed by one named requirement that states its release condition and its escape; a dispute is a condition, not a hold. No thread hold ever stops care | TH-06 |
| CR-5 | **PROPOSED.** Five case types now (credit balance, GFE dispute, recoupment, enrollment case, old-system balance); three names reserved | TH-08 |
| CR-6 | **PROPOSED.** A thread's amount is derived per station; with several counterparties it shows the largest and names it | TH-03 |
| CR-7 | **PROPOSED.** Re-association needs the keys required for the record's kind, including the appointment reference the EHR keeps where it keeps one, and any conflicting key excludes the thread; where the EHR keeps appointment references, facts carrying none never match a booked thread; a match is undoable for 30 days, but not once anything derived from it has left the building, when it becomes CL-08 correction work; anything else is work | TH-10, schema |
| CR-8 | **PROPOSED.** The action-type names that carry ruling 132's ceilings; any unnamed action type stays at `propose` | TH-13 |
| CR-9 | **PROPOSED.** The eighteen buckets | SL-02 |
| CR-10 | **PROPOSED.** What every ledger entry carries, including four dates | SL-03 |
| CR-11 | **PROPOSED.** Money before the service is a contract liability; money after it, not yet applied, is an unapplied credit | PY-03 |
| CR-12 | **PROPOSED.** The initial 48 kinds with their bucket pairs and neutral labels; the accountant confirms classifications | RT-02 |
| CR-13 | **PROPOSED.** The eight shipped service categories | RT-03 |
| CR-14 | **PROPOSED.** Until the accounting contract carries a service-category field (PA-1), no summary-journal batch with revenue lines from this change is released, and nothing folds the category into `reason_category` | RT-04 |
| CR-15 | **PROPOSED.** The receivable moves from unbilled to billed when a claim is accepted, not when it is sent; a rejection posts nothing. AI-15 says submission, so conforming amendment PA-7 is proposed | CL-12, RT-02; PA-7, T-18 |
| CR-16 | **PROPOSED.** One estimate per charge line; a charge finalizes even before an estimator is approved, and its deduction waits | RE-01 |
| CR-17 | **PROPOSED.** The first method: accountant-set ratios and constraints per payer class and service category. The constraint for a charge paid in full is the version's, with a shipped starting value of zero; the accountant decides whether exposure such as a Good Faith Estimate dispute that could force a refund needs one | RE-03 |
| CR-18 | **PROPOSED.** A cohort whose estimates miss by more than the version's threshold raises work and notifies the accountant | RE-08 |
| CR-19 | **PROPOSED.** The CPT overlay import records the practice's attestation; counsel's answer on CPT licensing is a go-live condition for CPT-coded items, as ruling 102 did for Hermes | SC-02 |
| CR-20 | **PROPOSED.** An estimate that a later price change makes stale raises work before the patient is billed | SC-03 |
| CR-21 | **PROPOSED.** The self-pay discount never applies automatically to a federal-program beneficiary who chooses to pay; the owner decides per visit | SC-04 |
| CR-22 | **PROPOSED.** What a reschedule re-checks, by what changed | AP-03 |
| CR-23 | **PROPOSED.** A missed-appointment fee is never charged automatically | AP-04 |
| CR-24 | **PROPOSED.** The claim-pathway, remittance and payment-processor contracts are defined by governed amendments of this change before features 3, 4, 5 and 6 need them | CP-05, PY-10, `tasks.md` §2 |
| CR-25 | **PROPOSED.** A booking dated after a later deactivation raises work and is not cancelled automatically, and an arrival on it is refused under PR-11 until a person reactivates the record or cancels the visit | AP-08 |
| CR-26 | **PROPOSED.** A booking outside recorded hours, or overlapping, warns and needs a reason; it is not refused | AP-06 |
| CR-27 | **PROPOSED.** A second identical charge line needs a coded reason | CG-06 |
| CR-28 | **PROPOSED.** A corrected or retracted fact behind a final line holds the thread with `clinical_fact_retracted` until every line using it is reversed or replaced, and raises that work for the biller | CG-08, CL-04 |
| CR-29 | **PROPOSED.** No partial estimate where another provider's or facility's items would have to be included; a work item instead | GF-01 |
| CR-30 | **PROPOSED.** The Good Faith Estimate's legal parameters live in a versioned set; counsel's recorded verification is required, and no estimate is issued without it | GF-03 |
| CR-31 | **PROPOSED.** A changed estimate is replaced, both kept; disputes measure against the last one issued before the service | GF-06 |
| CR-32 | **PROPOSED.** When coverage turns out to have ended, the self-pay discount applies before the first statement, which never shows the full charge | GF-11 |
| CR-33 | **PROPOSED.** Only a named human issues or replaces an estimate, at tier 2 | GF-12 |
| CR-34 | **PROPOSED.** A guarantor's credit is shown beside the balance and reduces it only when allocated | PS-01 |
| CR-35 | **PROPOSED.** Statements carry no diagnosis, clinical description or code | PS-03 |
| CR-36 | **PROPOSED.** Default statement terms of 30 days, set by the practice | PS-05 |
| CR-37 | **PROPOSED.** A statement shows a patient who is not the guarantor only as needed to identify the visit | PS-06 |
| CR-38 | **PROPOSED.** Sending statements from openPractice is tier 3 with a cancel window | PS-10 |
| CR-39 | **PROPOSED.** Until the material amount is set and confirmed, every write-off is tier 4 | PS-11 |
| CR-40 | **PROPOSED.** An undirected payment applies to the oldest undisputed charge lines first | PY-04 |
| CR-41 | **PROPOSED.** A credit-balance case's clock comes from a counsel-verified parameter per cause; until one exists, the case is due at once | PY-06 |
| CR-42 | **PROPOSED.** The fields of a hand-recorded eligibility check | EV-01 |
| CR-43 | **PROPOSED.** Participation is recorded per TIN, NPI, payer, payer product and location, never edited | EG-01 |
| CR-44 | **PROPOSED.** Held claims an approval does not cover need a named decision: correct the provider, convert to self-pay (named human only, discount first) or write off | EG-05 |
| CR-45 | **PROPOSED.** The participation hold's deadline escape releases only claims whose application is pending, narrowing FD-5 as the enrollment brainstorm proposes | EG-06; T-10 |
| CR-46 | **PROPOSED.** Commercial and secondary claims are recorded through the manual path in this change, so their receivables stay tracked | CL-02 |
| CR-47 | **PROPOSED.** The filing threshold defaults to 14 days before the deadline, set by the practice, with the cancel window ending before the deadline; no escape for a retracted fact | CL-06 |
| CR-48 | **PROPOSED.** A replacement or void without the original's payer control number is refused | CL-08 |
| CR-49 | **PROPOSED.** No acknowledgment within the adapter's declared window raises work | CL-09 |
| CR-50 | **PROPOSED.** A payer with no recorded filing window uses the shortest window recorded for any payer; with no window recorded for any payer, CR-79's default applies | CL-10 |
| CR-51 | **PROPOSED.** The simulated payer refuses any payer identity, and any patient, coverage, claim or charge record, not marked synthetic, and is unavailable on any site holding a patient record not marked synthetic | CP-03 |
| CR-52 | **PROPOSED.** The nine conditions that make a claim payment "exactly matched", including a current claim version, lines matched one to one, a claim payment equal to the sum of its lines with no claim-level adjustment, and no answer from the payer already posted for the claim version by claim control number and trace | RM-04 |
| CR-53 | **PROPOSED.** An automatic posting can be undone until its payment is explained in a deposit; after that a correction needs a person's reason | RM-04 |
| CR-54 | **PROPOSED.** Where a later coverage position exists, the patient's share waits for a person to decide whether a later payer is billed first | RM-07 |
| CR-55 | **PROPOSED.** A denied amount is never written off automatically; a person appeals it or writes it off | RM-07 |
| CR-56 | **PROPOSED.** A prepayment that will not be applied moves to unapplied credit with the disposition or finalization that makes it unappliable (`prepayment_to_credit`); whenever any posting leaves a charge line's patient receivable below zero, the amount below zero moves to unapplied credit with that posting (`patient_overpayment_to_credit`). Neither reverses or re-records the payment | PY-03, RT-02 |
| CR-57 | **PROPOSED.** A balance moves between guarantor accounts only as `guarantor_transfer`, by a named human, never below tier 2, with agents at `propose` only, under PR-15's own-record rule, and only to an account that is a guarantor of record for that visit | PS-12, SL-01, RT-02 |
| CR-58 | **PROPOSED.** Money that arrived before its remittance moves from unidentified receipts to the payer receivable (`unidentified_receipt_to_payer`) | RM-01, PY-06, RT-02 |
| CR-59 | **PROPOSED.** A payer's forwarding balance is carried in `refund_liability` and cleared when a later remittance recovers it; a recovery that matches nothing, or a forwarding balance in the practice's favor, is work | RM-08, RT-02 |
| CR-60 | **PROPOSED.** A bank debit matched to money paid out posts `bank_debit_explained`, so `cash_clearing` returns to zero; it needs the new label `bank_debit_clearing_release` (PA-1) | SL-13, RT-02; T-19 |
| CR-61 | **PROPOSED.** A patient write-off that reduces revenue first uses the charge line's remaining allowance (`patient_write_off_against_allowance`), only the excess reaches `revenue_deduction`, and no true-up counts it again. Both write-offs against the allowance carry the new label `write_off_against_allowance` (PA-1) | RT-09, RE-05, RT-02 |
| CR-62 | **PROPOSED.** The credit-loss method, a provision at close or a direct write-off to expense, is a field of the accountant's policy record, and no credit-loss entry posts until the accountant has chosen | RT-10, RE-06, RE-09, RT-02 |
| CR-63 | **PROPOSED.** A takeback that recovers an overpayment already posted clears `refund_liability` (`overpayment_recovered_by_takeback`) and is recorded as settled by offset, not as a refund the practice issued | RM-09, RT-02 |
| CR-64 | **PROPOSED.** The material test adds the write-off to every other write-off in the trailing 30 days on the same guarantor account, and separately on the same thread, and uses the larger total; it covers payer-side write-offs by the thread total | PS-11 |
| CR-65 | **PROPOSED.** Every entry an allocation posts names the same counterparty account on each side that carries one; what a guarantor owes moves only by `guarantor_transfer`, and money it paid only by correcting a misposted payment | SL-07, PY-04 |
| CR-66 | **PROPOSED.** Human tiers for recording and correcting actions no ruling tiered: a chargeback or returned item 2; identifying a payer overpayment 2; moving a credit to refund liability 2; classifying an unknown adjustment code 2; identifying an unidentified receipt 2; undoing an automatic posting 2; appealing a recoupment 2 and agreeing to one 4; printing a statement 1; reallocating a payment 2; each old-system import batch by a named human's sign-off; setting statement terms 2 | PY-09, RM-09, PY-07, RT-05, PY-06, RM-04, PS-04, SL-07, SL-11, PS-05 |
| CR-67 | **PROPOSED.** No real practice's money passes through openPractice in features 1 to 3. They are accepted and used with synthetic data only, because refunds, credit-balance cases and chargebacks arrive in feature 4 | `tasks.md` §2 |
| CR-68 | **PROPOSED.** The shipped write-off kinds are a starting classification for the accountant: price concessions, small balances and charity care reduce revenue after RT-09, and credit losses go to expense under RT-10. Whether charity care may be shown as a deduction from revenue or must be kept out of revenue altogether is the accountant's decision | RT-07, RE-09 |
| CR-69 | **PROPOSED.** When a visit with money paid toward it is cancelled, marked a no-show or left unseen, the prepayment moves to unapplied credit with the disposition, and the thread moves to `banked`, which also means "holds money paid toward the visit", and closes only when that credit is applied, refunded or moved to a credit-balance case. Undoing the disposition reverses that move, and is refused once the credit has been applied or refunded | AP-04, PY-03, TH-04 |
| CR-70 | **PROPOSED.** Legality is judged by the pair of stations, the occasions being examples. A condition-only transition is always legal; so is the reverse of a tier-1 or tier-2 move inside its undo window, and the undo of a minting transition, which closes the thread as `entered_in_error`. `seen` may close with a coded disposition. `booked` to `charged` is dropped, because a missed-appointment fee reopens the thread from `closed` | TH-04, AP-04 |
| CR-71 | **PROPOSED.** A move's undo window lasts until anything derived from it leaves the building, and never longer than 30 days | TH-07 |
| CR-72 | **PROPOSED.** A thread may carry several holds, each with its own reason, release condition, owner and clock. When conditions compete, the thread shows `disputed`, then `held`, then `stuck`; `disputed` ends when the case or appeal that opened it records its outcome or a withdrawal | TH-05, schema |
| CR-73 | **PROPOSED.** A claim is ready for the filing-deadline escape when it has no hold and no unreleased scrub failure, or when its only holds are those whose defining requirements name the escape (pending participation, an unresolved offering route, identity review after EB-17's attestation). The scrub records, and does not re-hold, a failure an escape has already released | CL-04, CL-06, EG-06 |
| CR-74 | **PROPOSED.** An authorization hold never releases by itself. At the filing threshold it escalates to a named human, who may release the claim into the batch with a recorded reason | EV-02, CL-06 |
| CR-75 | **PROPOSED.** A documentation hold never releases into a charge or a claim; at the filing threshold it escalates to the practice manager | CG-07, CL-06 |
| CR-76 | **PROPOSED.** No code, modifier or diagnosis pointer is prefilled or defaulted from a booked item, an earlier visit, a template or an agent's choice; in the manual case only a named human enters them; an agent drafts only from final EHR facts, copying them exactly and assigning none, drafts nothing in the manual case, and drafts nothing from a fact sent to same-person reconciliation; the catalog item is found from the code, never the reverse; the place of service comes from the location's record; an adapter's code edits only flag or hold, and its normalization tables apply only to codes payers send back | CG-01, CG-02, CG-03, GF-05, SC-01, CP-02 |
| CR-77 | **PROPOSED.** A claim recorded through the manual path is scrubbed and its result recorded; one sent while held, or repeating a line already claimed, is marked and escalated with the hold kept; only a named human records it | CP-04, CL-03 |
| CR-78 | **PROPOSED.** Feature 2 builds inside itself the narrow pieces it needs of the foundation's Feature 3: BAB-16's offering-route check at finalization and BAB-17's sign-off enforcement, rather than reordering the foundation's features (PA-3) | `tasks.md` §2.2 |
| CR-79 | **PROPOSED.** A filing window is stated in the payer's own unit and counted from the payer's own start date; the deadline is the end of its last day in the practice's time zone. With no window recorded for any payer, the practice's policy sets a conservative default, starting at 60 calendar days | CL-10 |
| CR-80 | **PROPOSED.** A payment hold does not refuse a walk-in's arrival at the desk; the hold is shown to staff | AP-07 |
| CR-81 | **PROPOSED.** A patient's request triggers a Good Faith Estimate only where counsel's parameter set says so; a gateway notice counts as delivery only where the parameter set accepts that channel and its acknowledgment is recorded; a representative receives one only within the proxy scope | GF-02, GF-04, GF-07 |
| CR-82 | **PROPOSED.** Booking is tier 1 | AP-02 |
| CR-83 | **PROPOSED.** Rescheduling is tier 1 | AP-03 |
| CR-84 | **PROPOSED.** Cancelling is tier 2 | AP-04 |
| CR-85 | **PROPOSED.** Recording an eligibility check is tier 1 | EV-01 |
| CR-86 | **PROPOSED.** A catalog, price, discount or category-mapping change is tier 2 | SC-08 |
| CR-87 | **PROPOSED.** Releasing every thread a met release condition frees, including claims freed by an enrollment, is tier 2 | EG-04, TH-06 |
| CR-88 | **PROPOSED.** Until the owner rules on them, these actions are done by a named human at tier 2 with a notification: recording a no-show or a patient who left unseen; closing a visit at `seen` with nothing to bill; undoing a minting; recording an authorization; releasing an authorization hold at the filing threshold; recording a participation record; deciding whether a later payer is billed first (CR-54); recording a claim sent outside the system and its acceptance; and recording that a signed refund was issued. Snoozing a stuck thread is tier 1 | `tasks.md` §3.5 |
| CR-89 | **PROPOSED.** Where a feature needs a capability another change's feature owns and that feature has not landed, the feature is built and accepted against a stand-in for it, and the part that needs it goes live only when the owning feature lands | `tasks.md` §2 |
| CR-90 | **PROPOSED.** Besides its release condition, a hold ends when a named human closes the work it guards (a write-off, a conversion to self-pay, or a `seen` to `closed` disposition), recording a reason at tier 2; a retracted-fact hold ends only by reversing or replacing the line, or by closing with no claim; a same-person reconciliation hold ends only when its work is closed | TH-06 |
| CR-91 | **PROPOSED.** When a person decides that a later payer is billed, the remaining amount moves from the earlier coverage's payer receivable to the later one's as `payer_share_transfer`, under that decision's governance, and the later claim's acceptance posts nothing more; it needs the new label `payer_responsibility_transfer` (PA-1) | RM-07, CL-12, SL-01, RT-02 |
| CR-92 | **PROPOSED.** No allocation takes a charge line's patient receivable below zero; at finalization a prepayment applies only up to what the guarantor then owes, the rest moving to credit; any posting that leaves a line below zero moves the amount below zero to credit | SL-07, PY-03 |
| CR-93 | **PROPOSED.** A chargeback or returned item undoes its payment in two parts: the allocated part as `chargeback` or `returned_item`, and the part still held in contract liability, unapplied credit or legacy collections by an SL-05 reversal against that bucket | PY-09 |
| CR-94 | **PROPOSED.** A fee posts by itself only from a matched processor or bank record; any other fee is recorded by a person at tier 2 with evidence; a deposit shortfall no record explains stays owned work in cash clearing; cash over and short has no kind until the drawer change (ruling 130) | SL-14 |
| CR-95 | **PROPOSED.** An unidentified receipt is linked to a remittance by itself only when the trace number and the amount are both equal; any other link is a person's work at tier 2 | RM-01, PY-06 |
| CR-96 | **PROPOSED.** Statements and Good Faith Estimate deliveries apply every restriction PR-15 carries across a same-person group, and never use an alternate channel recorded on another party of the group | PS-06, GF-07 |
| CR-97 | **PROPOSED.** The hold reason `same_person_reconciliation` holds a charge PR-15's same-person rules send to reconciliation; it never releases by itself and escalates at the filing threshold; finalization is refused while it is open, and agents draft nothing from such facts | TH-06, CG-03, CG-05, schema |
| CR-98 | **PROPOSED.** In customer-detail mode a line of a kind AI-13 does not yet map is held in openPractice and not sent; PA-1 proposes the postings | RT-04 |
| CR-99 | **PROPOSED.** Moving a payment recorded against the wrong guarantor account is an SL-05 correction made by a person at tier 2, with agents at `propose` and the own-record rule applying | SL-05 |

## Proposed amendments (for each owning change's owner; this change edits none)

Each amendment takes effect only when the owning change adopts it. Until then the owning text stands. PA-1 is required by ruling 125, and this change fails closed until it lands (CR-14). PA-2 to PA-10 are conforming: this change's requirements already bind without them.

**PA-1. `define-mvp-practice-foundation`: a service-category field, three labels, the bank-debit record and the customer-detail postings, in `openpractice-accounting-integration/1.0` (ruling 125). The gap is confirmed.** AI-15 says summary-journal mode posts totals "summarized by period, payer class, benign service category and account", and its roll-up groups "for a payer class and category". The canonical schema has no service-category field anywhere: not on `main` (digest `353492ce…`), and not as PR #8 amends it (digest `b15c4a19…`, unchanged from `4bce39a` to `f3bed0a`). `summaryLine` carries `reason_category`, `label` and `payer_class` but not a service category, and its `additionalProperties: false` refuses one. A destination therefore cannot receive the category axis AI-15 requires, except by folding it into `reason_category`, which ruling 125's two-axis list rules out. Three kinds this change ships also need labels the contract does not list: `write_off_against_allowance` (RT-09), `bank_debit_clearing_release` (SL-13) and `payer_responsibility_transfer` (RM-07). The amendment applies on top of PR #8's schema at `f3bed0a`. It was built from that schema, checked against the JSON Schema 2020-12 meta-schema, and tested with thirteen synthetic summary lines, each of which passed or failed as intended.

**Compatibility.** This amendment is not backward-compatible. Every summary batch valid under the current schema becomes invalid, because the batch's map version is required and every revenue or deduction line needs a category, a payer class and a group level. Every batch valid under the amended schema fails the current one. It is therefore acceptable only as an edit made in place to `openpractice-accounting-integration/1.0` while that contract is an unratified draft, with no adapter built and no batch in existence. After that, the contract's own rule that a 1.x reader accepts any 1.x record would make the same edit a new MAJOR version.

**The estimate reference.** AI-15's sentence is narrowed so that only gross-to-net lines carry the estimate reference, and the schema's existing `revenueLabel` rule that requires it is kept unchanged. A revenue-reducing write-off is a person's realized decision, not the application of an estimate: under RT-09 it posts first against what the allowance holds, and only its excess reduces revenue. An estimate reference on it would point at an estimate that does not explain the line. Its evidence is the write-off record in openPractice's detail manifest.

| Part | Current | Proposed |
|---|---|---|
| Schema `$defs.summaryLine.properties` | No such property | Add `"service_category": { "description": "Benign service category of a line that is revenue or a deduction from revenue, from the practice's versioned service-category mapping (define-revenue-cycle-core RT-03). Absent from every other line, and never present in customer-detail postings (AI-16).", "type": "string", "pattern": "^[a-z][a-z0-9_]{0,63}$" }` |
| Schema `$defs` | No such definition | Add `"revenueOrDeductionLabel": { "description": "Labels of lines that are revenue or a deduction from revenue. Each such line carries a service category, a payer class and a group level.", "enum": ["patient_services_revenue", "gross_to_net_deduction", "gross_to_net_true_up", "write_off"] }` |
| Schema `$defs.summaryLine` | One `if`/`then` on `revenueLabel`, requiring `estimate_ref`, `payer_class` and `group_level` | Keep that `if`/`then` unchanged, and add `"allOf": [ { "if": { "properties": { "label": { "$ref": "#/$defs/revenueOrDeductionLabel" } }, "required": ["label"] }, "then": { "required": ["service_category", "payer_class", "group_level"] }, "else": { "properties": { "service_category": false } } } ]` |
| Schema `$defs.neutralLabel.enum` | 28 labels, ending `"deposit_clearing_release"` | Append `"write_off_against_allowance"`, `"bank_debit_clearing_release"` and `"payer_responsibility_transfer"` |
| Schema `$defs.neutralLabel.description` | Ends "…and openPractice remains the single deposit poster." | Append: " write_off is a write-off that reduces revenue; write_off_against_allowance realizes an amount the gross-to-net allowance already held back and has no revenue effect. bank_debit_clearing_release releases cash clearing against an explained bank debit line, such as a refund disbursement, a chargeback or a payer takeback taken from the bank, and is sent only to a destination whose AI-20 record says openPractice sends such lines. payer_responsibility_transfer moves an amount from one payer's receivable to the payer in the next coverage position, with no revenue effect." |
| Schema `$defs.summaryBatch` | Second `allOf` member requires `["summary_lines"]` | Add `"service_category_map_version": { "description": "Version of the practice's whole effective-dated service-category mapping, which holds every remap with its effective date, so that one batch spanning a remap is covered by one version.", "type": "integer", "minimum": 1 }` to that member's `properties`, and make its `required` `["summary_lines", "service_category_map_version"]` |
| Schema `$defs.line` (customer-detail) | `additionalProperties: false`, no category | Unchanged, so a customer-detail line can never carry a category (AI-16). Its `label` refers to `neutralLabel`, so the three new labels become available to it |
| AI-15 | "Each line SHALL carry a reason category, a neutral label, an amount, a posting date and a batch reference, and SHALL carry a payer class where it touches a receivable or revenue. A revenue or deduction line SHALL carry the estimate version or close snapshot it applies, by reference." | "Each line SHALL carry a reason category, a neutral label, an amount, a posting date and a batch reference; SHALL carry a payer class where it touches a receivable or revenue; and SHALL carry its benign service category and its group level where it is revenue or a deduction from revenue, and a service category on no other line. A gross-to-net line, meaning a patient-services revenue, gross-to-net deduction or gross-to-net true-up line, SHALL carry the estimate version or close snapshot it applies, by reference. Each batch SHALL record the version of the practice's whole effective-dated service-category mapping that it used." |
| AI-08 | "The report SHALL compare openPractice totals with the destination's totals for documents carrying openPractice keys: by reason category, and in customer-detail mode also by customer." | "The report SHALL compare openPractice totals with the destination's totals for documents carrying openPractice keys: by reason category; in summary-journal mode also by service category, on lines that are revenue or a deduction from revenue; and in customer-detail mode also by customer." |
| BAB-06 | "Each destination SHALL map reason categories to its existing accounts through an account mapping that is versioned per destination and per posting mode." | "Each destination SHALL map reason categories, and for summary-journal lines that are revenue or a deduction from revenue each pair of reason category and service category, to its existing accounts through an account mapping that is versioned per destination and per posting mode." |
| AI-21 | Bullet "- summary-journal control-account reconciliation by payer class;" | Add a bullet after it: "- the service category on every summary-journal line that is revenue or a deduction from revenue, its absence from every other line and from every customer-detail posting, and the mapping version on every batch;" |
| AI-20 | Its last paragraph ends "…and neither SHALL be opted in for agent signing (BAB-17)." | Add a paragraph after it: "Before any destination goes live, in either posting mode, openPractice SHALL also record, per destination, who books the bank debits that openPractice explains: either openPractice sends explained-bank-debit lines, with label `bank_debit_clearing_release`, or the destination books bank debits from its own bank feed and codes each against the account mapped to `cash_clearing`. Until that record exists, no explained-bank-debit line SHALL be sent. If the record later changes to openPractice sending them, only debits explained after its effective date SHALL be sent." |
| BAB-14 | "Each refund disbursement line SHALL be matched to the issued refund it pays out (BAB-07). The accounting system SHALL own bank balances and the full bank reconciliation." | "Each refund disbursement line SHALL be matched to the issued refund it pays out (BAB-07). Who books an explained bank debit in the destination SHALL follow that destination's record under AI-20; where the destination books it from its own bank feed, it SHALL code it against the account mapped to `cash_clearing`, so that the refund, fee and chargeback lines openPractice has already posted are not doubled. The accounting system SHALL own bank balances and the full bank reconciliation." |
| AI-13 table | Eight rows, ending "An unidentified receipt identified later" | Add these rows: "\| A prepayment that will not be applied \| No posting: in customer-detail mode the prepayment is already a customer credit (`payment.post` as an unapplied payment) \|"; "\| A payment above what a charge line now owes \| A `reversal.post` of that part of the payment's `payment.apply`, leaving it as the customer's unapplied credit \|"; "\| A balance moved to another guarantor account \| A `credit_note.post` on the first customer's invoice and a `debit_note.post` to the second customer, both with label `patient_responsibility_transfer` and linked by `applies_to` \|"; "\| A balance passed to the payer in the next coverage position \| A `journal.post` moving the amount from the earlier payer's receivable to the later payer's, with label `payer_responsibility_transfer` \|"; "\| An unidentified receipt identified as a payer's payment \| A `journal.post` that releases the unidentified-receipts liability, plus a `payment.post` on the payer, both linked by `applies_to` to the original unidentified-receipt posting \|"; "\| A takeback that recovers an overpayment already owed back to the payer \| A `journal.post` against the refund-liability account, with label `payer_takeback`, and never a `reversal.post`, which would reopen the receivable in the destination \|"; "\| A bank debit that openPractice has matched \| Only where the destination's record under AI-20 says openPractice sends them: a `journal.post` releasing the clearing account, with label `bank_debit_clearing_release` \|"; "\| A write-off against the allowance \| A `credit_note.post` with label `write_off_against_allowance` \|"; "\| A credit loss written off directly \| A `credit_note.post` with label `credit_loss` \|". A forwarding balance needs no new row: the existing provider-level-offsets row already carries it |
| That change's `design.md`, customer-detail table | Row "\| Write-off \| `credit_note.post` with label `write_off` \|" | "\| Write-off \| `credit_note.post` with label `write_off`, or `write_off_against_allowance` where it realizes an amount the allowance already held back (`define-revenue-cycle-core` RT-09) \|" |
| `contracts/SHA256SUMS` and the digest citations in that change's design | `353492ce…` on `main`, or `b15c4a19…` as PR #8 amends it | The new digest, computed on the file as that change writes it |

**PA-2. `define-actors-agents-and-work-inbox` WI-07 (conforming).** Current: "The inbox SHALL present the gates of `define-mvp-practice-foundation` as work items." Proposed: "The inbox SHALL present the gates of `define-mvp-practice-foundation`, and the holds, sign-offs, proposals and cancel windows of `define-revenue-cycle-core` (TH-12, CG-04, CL-05, CL-06, RM-05, PS-11, PY-07, GF-12, EG-05), as work items." TH-12 already binds through WI-01.

**PA-3. `define-mvp-practice-foundation` `tasks.md` 2.3 and 2.4 (conforming).** In 2.3, current: "Two prerequisites must be complete first, each under its own governance: the FD-7 Thread contract must be versioned, and the money ledger and reason taxonomy must be defined." Proposed: append "Change `define-revenue-cycle-core` defines all three, and its feature 1 must be merged first; PA-1 of that change must also be adopted before go-live." In 2.4, current: "The owner may fold this feature into the Slice 3 charge-capture feature, provided ownership is recorded." Proposed: "This feature is folded into change `define-revenue-cycle-core`, which owns its acceptance evidence: that change's feature 2 builds the first case of EB-17, manual entry where the practice has no linked EHR, and its feature 3 builds the rest of EB-13 to EB-18. Ownership is recorded in both changes." And, for design call CR-78, append to 2.3: "Feature 2 of `define-revenue-cycle-core` builds, inside itself and before this feature, the offering-route check of BAB-16 at charge finalization and the sign-off enforcement of BAB-17 that its own sign-offs need; this feature adopts them rather than building them again, and owns the rest of BAB-01 to BAB-17."

**PA-4. `define-customer-financial-data-boundaries` "Offerings determine financial routing at line level" (conforming).** Current first sentence: "Each financial offering SHALL identify its effective version, selling entity, billing route, authoritative obligation owner, category, currency, approved accounting/tax mapping and disclosure profile." Proposed: add after it, "For an insurance-billed or direct-pay healthcare service, the offering is the catalog item of `define-revenue-cycle-core` SC-01, which carries these facts (SC-06)."

**PA-5. `define-practice-readiness` inventory (conforming; PR #10 is open).** Its checklist follows the requirements, so once this change is ratified it should gain rows for: the Good Faith Estimate parameter set's counsel verification (GF-03); counsel's answer on the CPT overlay (SC-02); the material write-off amount confirmed by the accountant (PS-11, RE-09); the credit-balance clock parameters (PY-06); the clearinghouse agreement (CP-05); the payer filing windows (CL-10); and, in the accountant's policy record, the credit-loss method (RT-10), the presentation of charity care (RT-07) and the rule of the material test (PS-11). The first estimator version is already its RI-02.

**PA-6. `add-reimbursement-retention-control` (conforming).** Its policy profiles should name, among their covered record families, the claim as submitted, the remittance as received, the Good Faith Estimate as issued with its delivery record, the statement as issued and the receipt as issued (GF-08, RM-11). Its requirements carry no IDs; the target is "Retention policy profiles are versioned and sourced".

**PA-7. `define-mvp-practice-foundation` AI-15, for design call CR-15 (conforming).** AI-15's summary covers, in its current bullet, "- claim submission: reclassification from unbilled to billed receivable, with no revenue effect;". CL-12 moves the receivable when the payer or clearinghouse accepts the claim, not when it is sent, because a rejected claim never became a payer's debt (CR-15). Proposed bullet: "- claim acceptance by the payer or clearinghouse: reclassification from unbilled to billed receivable, with no revenue effect; a rejected claim causes none;". Until it is adopted, the two texts disagree only on timing, and this change follows CL-12 (T-18).

**PA-8. `define-patient-gateway-and-escalation` `tasks.md` 2.3 (conforming).** Current gate of its payment-hold feature: "Gate: Feature 1 merged, and patient receivables available from `define-mvp-practice-foundation` Feature 3 with its prerequisites (the FD-7 Thread contract and the money ledger)." Proposed: "Gate: Feature 1 merged, and patient receivables available from `define-revenue-cycle-core` features 1 and 2: the thread contract, the money ledger, and the guarantor-account balances and balance events of PS-01 and PS-09." Payment holds read balances, not the books, so they need not wait for the accounting feature.

**PA-9. `define-actors-agents-and-work-inbox` AO-11 and design call D-15 (conforming).** AO-11's current sentence: "Each answer type SHALL be available only once the openPractice data it reads exists under a ratified change: appointment times wait for a ratified scheduling change, and balances for the patient receivables of `define-mvp-practice-foundation` (design call D-15)." Proposed: "…appointment times wait for the booking of `define-revenue-cycle-core` (AP-01 to AP-09), and balances for its guarantor-account balances (PS-01, PS-09) (design call D-15)." D-15's row should say the same. This change's AP-09 and PS-09 already state when each answer may be switched on.

**PA-10. `define-patient-gateway-and-escalation` PH-01, for design call CR-80 (conforming; the owner decides CR-80).** PH-01's list of exceptions is closed. Current sentence: "No one in openPractice can mark an appointment clinically needed." Proposed: add after it, "Recording the arrival of a patient who is present without a booking, a walk-in, is not new scheduling, and a hold SHALL NOT refuse it (`define-revenue-cycle-core` AP-05, AP-07)." Until it is adopted, or if the owner rejects CR-80, AP-07's walk-in sentence yields to PH-01 as written.

## Conflicts and tensions

| # | Tension | Status |
|---|---|---|
| T-1 | The workflow protocol hands one change to exactly one Speckit feature | **Ruled** by ruling 112, as ruling 1 did for the foundation; the owner confirms the hand-off shape in `owner-ratification.md` |
| T-2 | BAB-02 bars a general ledger, yet every entry here debits one bucket and credits another | **Resolved** by ruling 124. The eighteen buckets are fixed, cannot be authored, and are mapped to the destination's accounts (BAB-06); openPractice produces no financial statement |
| T-3 | Ruling 33 says automation stays propose-only; ruling 120 lets exactly matched remittance lines post by themselves | **Recorded.** Ruling 120 is the later and more specific ruling. RM-04 posts at tier 2 with notice and undo, and only under CR-52's narrow definition |
| T-4 | FD-5 sends claim batches at tier 3 after a cancel window; ruling 119 requires an explicit release by a named signer | **Conscious exception** by ruling 119. It also avoids risk R-2 (tier 3 against Principle II), as AI-06 did for export batches |
| T-5 | The framework's §5A.2.5 ranks fee schedules and contract terms as the strongest estimator evidence; FD-2 makes the learned baseline primary; ruling 126 loads no fee schedules | **Resolved** for the first method by ruling 126: accountant-set ratios (RE-03). A learned method is a later version |
| T-6 | FD-5 relies on reversibility below tier 4; ruling 116 adds a named biller's confirmation before a charge is final | **Conscious exception** by ruling 116, like the foundation's T-17 |
| T-7 | The visit-thread brainstorm makes queues saved filters, not objects; WI-01 keeps one queue of work items | **Resolved**: holds and stuck threads appear as WI-01 work items (TH-12), and WI-01 governs |
| T-8 | PH-01's scheduling clause waits for "a ratified scheduling change" | **This change is that change** once ratified; AP-07 applies the clause with its exceptions |
| T-9 | CL-06's escape releases a claim with no affirmative click, which is risk R-2's case | **Recorded.** Ruling 119 keeps the escape, EB-18 already states it, and CR-47 bounds it |
| T-10 | FD-5 gives every gate a deadline escape; EG-06 refuses the escape for denied or unknown participation | **PROPOSED narrowing** (CR-45), fail-closed for a claim going out; the owner decides |
| T-11 | The framework's §9.1 makes the MVP Medicare-first and omits the Good Faith Estimate; FD-4 puts the self-pay path first | **Resolved** by rulings 113 and 114: self-pay first, then Medicare Part B |
| T-12 | The self-pay brainstorm's reason table includes retail sales and sales tax | **Excluded** by rulings 24 and 26 (SC-06) |
| T-13 | Claim submission and remittance access are protected operations of `add-reimbursement-retention-control`, which hands off to its own single feature | **Ordering dependency**: feature 3 needs that feature landed, or must build against its contracts (`tasks.md` §2.3) |
| T-14 | AI-15 summarizes by service category, but the accounting schema has no field for it | **Confirmed gap**; PA-1. Fail-closed meanwhile (CR-14) |
| T-15 | FD-3 lists the daily balanced day; the foundation claims nothing about it | **Split** by ruling 130: the deposit slip here (PY-05); the drawer count and close sheet in a later change |
| T-16 | Rulings 107, 123 and 129 need the practice's accountant to sign, but the roster has no actor type or recipe for one (`define-practice-readiness` I-1, RI-51, pending there as owner decision 141; decisions 139 and 140 there concern who may give the accountant's sign-off) | **Recorded; fail-closed.** No estimator version, policy record or material amount can be approved until those are settled, so accounting cannot go live (RE-02, PS-11). Building the features is not blocked |
| T-17 | The foundation's `tasks.md` 2.3 asks for the Thread contract and the money ledger "each under its own governance" | **This change** is that governance (PA-3) |
| T-18 | AI-15 reclassifies unbilled to billed at claim submission; CL-12 does it at acceptance (CR-15) | **PROPOSED conforming amendment** PA-7; the owner decides CR-15 |
| T-19 | BAB-14 makes openPractice the single poster of each deposit it explains, but says nothing about who books an explained bank debit; SL-13 posts `bank_debit_explained` in openPractice's ledger | **PROPOSED resolution in PA-1.** openPractice's own ledger needs the entry either way. AI-20 gains a per-destination record, required before go-live in either mode: openPractice sends explained-bank-debit lines, or the destination books bank debits from its own feed against the account mapped to `cash_clearing`. No such line is sent until the record exists |
| T-20 | FD-5 gives every gate a deadline escape; CG-07 and EV-02 let documentation and authorization holds only escalate at the deadline, never release into a claim by themselves | **PROPOSED narrowing** (CR-74, CR-75), like T-10: fail closed for a claim going out. The owner decides |
| T-21 | Ruling 41 lets a payment hold block "new non-urgent scheduling"; a walk-in creates an appointment record when the patient arrives | **PROPOSED reading** (CR-80): recording the arrival of a patient who is present is not new scheduling, so a payment hold does not refuse it. The owner decides |

## Dependencies

- **On `main`:** the five merged changes, each draft.
- **Draft PR #7**, read at `e746f58`: EB-13 (confirmed diagnoses only) and EB-15 (retractions with a reason code, both codes with the same billing effect, unfinalized charges held, staff choosing a corrected or voided claim), cited by CG-02, CG-05 and CG-08.
- **Draft PR #8**, re-checked at `f3bed0a`: PR-15 (one party per person, roles and links, the rule for a staff member acting on their own record, the reversible same-person link and its groups, the held contact when a restriction leaves no permitted channel, the early double-charge safeguard, and the hold on a staying record's charge) and design call F-7 (its one-person alternative), cited by SL-06, CG-04, CL-05, PS-06, PS-11, PS-12, PY-08, GF-07 and TH-09; BAB-18 (decimal text and a currency code on the wire, whole cents in storage, one rounding rule, cumulative rounding for splits in a stated or default order, exact arithmetic) and design calls F-8 to F-10, cited by SL-04, SL-07, RE-03 and SC-04 and the thread schema; AM-01 (each human roster entry references one party) and AO-04 as amended for ruling 100, cited by TH-13.
- **Draft PR #10**, re-checked at `d4724e1`: SR-02 (the role on every sign-off) and SR-04 (no self-approved accountant or counsel sign-off), with design call RD-11's engagement test, cited by RE-02, GF-03, CG-04 and CL-05; and its findings I-1 and RI-51, pending there as owner decisions 139 to 141 (T-16).
- This change should merge after PRs #7, #8 and #10.

## Risks / Trade-offs

- **[A large change is reviewed as one]** → Ruling 112 accepts it; the six features keep the build incremental, and `tasks.md` gates each on the last.
- **[The shapes are wrong and discovered late]** → The self-pay slice exercises every shape before any payer is involved (ruling 114), and the thread contract has one MAJOR break budget (TH-11).
- **[A patient is told a wrong amount]** → Estimates, statements and conversions to self-pay need a named human or a cancel window, and the discount is applied before any statement where coverage ended (GF-11, PS-10, EG-05).
- **[A claim goes out wrongly]** → Explicit release (CL-05), scrub (CL-04), frozen submissions (CL-07), and an escape that never fires for retracted facts or denied participation (CL-06, EG-06).
- **[Money moves without authority]** → openPractice records money and never moves it; refunds are tier 4 (PY-01, PY-07).
- **[Legal parameters are wrong]** → No Good Faith Estimate, CPT activation or credit-balance clock rests on an unverified parameter (GF-03, SC-02, PY-06).
- **[Nobody can approve the estimator]** → Recorded as T-16; accounting waits, and billing does not.
- **[Two contracts drift]** → The thread schema is pinned by digest, and PA-1 changes the accounting schema only through its owner.

## Migration Plan

1. Validate strictly. The owner completes `owner-ratification.md`, confirming the six-feature hand-off (ruling 112), after PRs #7, #8 and #10 have merged.
2. The foundation's Feature 1 (registry), then this change's feature 1 (foundations), with the foundation's Feature 2 (EHR link) alongside.
3. Feature 2 (self-pay visit). Counsel verifies the Good Faith Estimate parameter set before any estimate is issued in production.
4. The foundation's Feature 3 (accounting), after feature 1. It goes live only once PA-1 is adopted and the accountant has approved the policy record and the first estimator version.
5. Features 3 to 6 in order, each after its adapter contract is defined (CR-24).
6. The old-system balance import before the first practice with legacy balances goes live (ruling 131).
7. Rollback disables an adapter or a feature's entry points and holds undelivered work. It never deletes ledger entries, threads, claims as submitted or evidence.
8. Archive only after all six features land, the root pin bump lands, and `closure-evidence.md` is complete (ruling 109).

## Open Questions

Every design call above is an open question for the owner, with its PROPOSED answer as the recommendation. These are the ones that most change what the practice experiences, and the questions that need someone outside the product.

**For the owner** (each with this change's recommendation)

1. Should the thread be a contract with a schema and digest, like the integration contracts (CR-2)? Recommendation: yes, so FD-7's "before any consumer" can be checked.
2. Do you accept the eighteen buckets and 48 kinds as the starting list, with your accountant confirming the classifications (CR-9, CR-12)? Recommendation: yes.
3. Until the accounting contract gains the service-category field and the two labels, should every summary-journal batch carrying this change's revenue be refused, which blocks accounting go-live, rather than fold the category into another field (CR-14, PA-1)? Recommendation: yes; fail closed, and route PA-1 to the accounting contract's owner now.
4. Should the receivable move from unbilled to billed when the payer or clearinghouse accepts a claim, rather than when it is sent as AI-15 now says (CR-15, PA-7, T-18)? Recommendation: yes, and adopt PA-7: a rejected claim never became a payer's debt, and booking it at submission would show a receivable the payer refused.
5. Should commercial and secondary claims be recorded through the manual path until a later feature builds them (CR-46)? Recommendation: yes, so no receivable goes missing.
6. Is 14 days before the filing deadline the right default for the claim escape (CR-47)? Recommendation: yes, practice-adjustable.
7. Should the participation hold's escape refuse to send claims whose application was denied or never made (CR-45)? Recommendation: yes.
8. Do you accept the eight conditions for posting a remittance automatically, including a current claim version, lines matched one to one, and no answer already posted for the claim version (CR-52)? Recommendation: yes.
9. Ruling 120 says an automatic posting is undoable. CR-53 allows a plain undo until the payment is explained in a deposit, and after that a correction with a person's reason. Do you accept that limit (CR-53)? Recommendation: yes. A plain undo after the deposit is explained would reopen a deposit already matched to its bank line; the correction still reverses everything the posting made, so the posting stays reversible in substance, with a reason on record.
10. Should a patient's share wait when a second coverage exists, and should denials never be written off automatically (CR-54, CR-55)? Recommendation: yes to both.
11. Should openPractice issue no Good Faith Estimate until counsel has verified its parameters (CR-30)? Recommendation: yes; the practice handles estimates outside the system meanwhile.
12. Until the material write-off amount is set and confirmed, should every write-off need a named human (CR-39)? Recommendation: yes.
13. Should a balance move between guarantor accounts only by a named human's `guarantor_transfer`, never below tier 2, with agents only proposing (CR-57)? Recommendation: yes.
14. No real practice's money passes through features 1 to 3 (CR-67), because refunds arrive in feature 4. Should refund recording move into feature 2: PY-07's tier-4 record of a refund issued, `patient_credit_to_refund` and `refund_issued`, all of which move no money? Recommendation: yes, keeping CR-67 either way. It is small, and it lets the self-pay slice finish a cancelled prepaid procedure end to end; matching the refund's bank debit (SL-13) stays with feature 4.
15. When will the accountant's place on the roster be settled (T-16; `define-practice-readiness` RI-51, pending there as your decisions 139 to 141)? Recommendation: decide them before the foundation's accounting feature goes live, and have the owner of `define-actors-agents-and-work-inbox` carry the actor type, because every accountant approval in this change waits on it.
16. Which feature builds the old-system balance import (ruling 131)? Recommendation: feature 4, or earlier if the first practice has legacy balances.
17. Feature 2 needs the offering-route check of BAB-16 and the sign-off enforcement of BAB-17, which the foundation's accounting feature owns and which comes after feature 2. Should feature 2 build those two narrow pieces inside itself, with PA-3 recording it, rather than reordering the foundation's features (CR-78)? Recommendation: yes; the accounting feature then adopts them.
18. Should a payment hold leave a walk-in's arrival at the desk alone, showing the hold to staff, on the reading that recording a patient who is present is not new scheduling under ruling 41 (CR-80, T-21)? PH-01's list of exceptions is closed, so this needs PA-10 adopted in the gateway change. Recommendation: yes, and adopt PA-10.
19. When a prepaid visit is cancelled, missed or left unseen, should its thread wait at `banked` until the patient's credit is applied, refunded or moved to a credit-balance case, rather than close while the practice owes the patient (CR-69)? Recommendation: yes.
20. Should documentation holds and authorization holds only escalate at the filing deadline, never release into a claim by themselves, with a named human able to release an authorization hold with a recorded reason (CR-74, CR-75, T-20)? Recommendation: yes; both would otherwise send a claim the payer is likely to deny.
21. Do you accept tier 2, done by a named human with a notification, for the recording actions listed in `tasks.md` 3.5, and tier 1 for snoozing a stuck thread, until you rule on each (CR-88)? Recommendation: yes, and rule on them at ratification.

**For counsel**

22. What are the current Good Faith Estimate triggers, deadlines, required contents, dispute threshold, dispute window and retention period for this practice; does a patient's request, and not only scheduling, trigger an estimate; which delivery channels count, including a notice through the patient-app gateway; and does the convening-provider rule ever apply (GF-03, CR-29, CR-81)? The self-pay brainstorm's figures are design assumptions it flags for verification.
23. May a practice import its own CPT content into a self-hosted product, and is the practice's attestation enough (SC-02; FD-11, risk R-1)?
24. May the self-pay discount be offered to a federal-program beneficiary who chooses to pay, and on what conditions (SC-04, CR-21)?
25. What does a patient statement have to contain or avoid in the practice's state, and what collection rules apply to a medical practice billing its own patients (PS-03)?
26. Where a minor may consent to a service confidentially under state law, may that service's administrative name appear on a statement addressed to a parent guarantor, and must it instead be left off, billed elsewhere or held (PS-03, PS-06)?
27. What clocks govern a payer overpayment, a patient credit and an unidentified receipt, including the 60-day report-and-return rule the framework cites at 42 CFR 401.305 and state unclaimed-property rules (PY-06, RM-09)?
28. What retention periods apply to claims as submitted, remittances, statements, receipts and estimates (GF-08, PA-6)?
29. What must the clearinghouse agreement contain (CP-05)?

**For the practice's accountant**

30. Approve the policy record: reporting basis, estimator, receivable classification, materiality and reopening (RE-09, D8).
31. Confirm how each kind maps to the books, especially the self-pay discount, price concessions and the allowance kinds (RT-02, RT-07, CR-12).
32. Should charity care be shown as a deduction from revenue, as the shipped `charity_care` kind does, or kept out of revenue altogether (RT-07, CR-68)?
33. Which credit-loss method does the practice use: the allowance method, with a provision at each close from rates you set, or the direct method, writing uncollectible balances straight to expense (RT-10, CR-62)? Nothing posts for a credit loss until you choose.
34. Do you accept that a patient write-off first uses what the estimate held back, with only the excess reducing revenue, and how should the allowance part be presented when the write-off was charity care (RT-09, CR-61; the second worked example in Decision 3)?
35. Set the first method's ratios and constraints per payer class and service category, the conservative values for payers with no history, and the back-testing threshold (RE-03, RE-04, RE-08).
36. Should a charge paid in full carry a collectibility constraint, given that a Good Faith Estimate dispute can still force a refund? The shipped starting value is zero (RE-03, CR-17).
37. Confirm the material write-off amount the practice sets, and that the test adds every write-off on the same guarantor account in the trailing 30 days (PS-11, CR-64).
38. Confirm that money received before a service is a contract liability (PY-03).
39. When a balance is found uncollectible on a charge line that still has allowance, should the credit loss first absorb that allowance, as a patient write-off does under RT-09, or go wholly to credit-loss expense while the close re-estimate releases the allowance (RT-10, RE-06)?
40. For each destination, should openPractice send the bank debits it matches to refunds, chargebacks, takebacks and fees, or should the destination book them from its own bank feed against the account mapped to cash clearing (SL-13, T-19, PA-1's AI-20 record)? Nothing is sent until that record exists.

**For a certified biller**

41. For Medicare Part B as primary: which jurisdiction and contractor, which enrollment and submission channel, and which acknowledgment windows apply (CP-06, CL-09)?
42. What are the filing windows of the practice's payers (CL-10)?
43. Do the practice's payers carry and recover forwarding balances as RM-08 describes, and how should payer-level adjustments outside RT-02's kinds be handled (RM-08, CR-59)?
44. How often do the practice's patients need secondary claims, and does Medicare cross them over automatically for this practice (CL-02, CR-46, CR-54)?
45. Do the practice's payers backdate enrollment, and is a 14-day escape enough for their filing windows (EG-04, CR-47)?
46. Which remittances still arrive on paper (RM-02)?
47. When a claim is still waiting on an authorization at the filing threshold, in what cases do the practice's payers accept the claim anyway or grant a retroactive authorization, so that a named human may release it (EV-02, CR-74)?
48. How do the practice's payers state their filing windows (days, months or years, and counted from the date of service or from an earlier payer's answer), and is 60 calendar days a safe default where no window is recorded at all (CL-10, CR-79)?
