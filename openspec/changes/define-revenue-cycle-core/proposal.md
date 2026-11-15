Status: draft

Ratification is pending. The owner has not ratified this change; see `owner-ratification.md`.

## Why

openPractice exists to turn a visit into money: a booked visit becomes a priced charge, the charge becomes a claim or a patient bill, the payer's answer and the patient's payment settle it, and the books receive a balanced summary. The five changes merged on `main` specify the edges of that path: the patient registry, the EHR link, the accounting contract, agent governance, the patient-app gateway and payment holds. They do not specify the path itself. There is no requirement yet for the visit thread, the appointment, the service catalog and prices, charges, claims, remittances, the money ledger, the reason codes, patient balances, statements or Good Faith Estimates.

Merged requirements already wait on these. The foundation change holds its accounting feature until "the FD-7 Thread contract must be versioned, and the money ledger and reason taxonomy must be defined" (`define-mvp-practice-foundation` `tasks.md` 2.3), and holds its clinical-facts feature until the Thread contract exists (2.4). Its proposal lists "The Thread contract, the reason taxonomy's categories" as non-goals. The payment-hold rules count balances, disputes and Good Faith Estimate dispute windows (PH-02, PH-04, PH-06, PH-07, PH-09) that nothing defines, and PH-01's scheduling clause waits for "a ratified scheduling change". The agent answer about a patient's balance waits for the receivables to exist (AO-11).

The written critique of 2026-11-15 named this gap ("The Missing Middle"). The product owner, Brett Heap, ruled on 2026-11-15 that the revenue-cycle core is written next and perimeter work is paused (ruling 110), and then answered decisions 112 to 132 on its scope "for the rest, go with your recomendations". Those rulings are recorded in `design.md` under "Owner rulings", with the earlier rulings this change relies on. **Rulings are not ratification:** the change stays draft until the owner completes `owner-ratification.md`, and under rulings 109 and 110 nothing is handed to build before that.

Paths are relative to the root of `opensoft/openPractice-spec`.

## What Changes

The change adds one money path, built as six features in a fixed order (ruling 112). Payers come in a fixed order too: self-pay first, then Medicare Part B as the primary payer, with commercial and secondary claims later on the same data shape (ruling 113). Every step that ruling 112 to 132 does not settle is a design call marked PROPOSED in `design.md`, kept fail-closed where it touches money leaving the practice, a claim going out, or a patient being told an amount.

- **The visit thread (`encounter-thread`, TH-01 to TH-16).** One durable record per visit the practice means to happen, and per enumerated non-visit case (a credit balance, a Good Faith Estimate dispute, a recoupment, an enrollment case, a balance carried over from an old system). It carries a station, a condition, an age, a clock, an amount at risk and an owner. Every move is dated, attributed, justified and never rewound. Holds and stuck threads become work items. The thread is written as the versioned contract `openpractice-thread/1.0`, with a canonical schema, before anything reads it (FD-7). It also states the agent ceilings of ruling 132 for every action type the change defines.
- **The administrative appointment (`administrative-appointment`, AP-01 to AP-09).** openPractice books, reschedules, cancels and records no-shows and walk-ins, each as a move on the visit thread (rulings 31 and 115). No reminders, waitlist or self-scheduling. Booking applies PH-01's payment-hold clause with its exceptions.
- **The service catalog and prices (`service-catalog-and-pricing`, SC-01 to SC-08).** One catalog item per service, one practice price list read by date of service, and one self-pay discount (ruling 126). Each item maps to a shipped service category and carries the offering-route facts that BAB-16 and the customer-financial change need. No payer fee schedules are loaded.
- **Charges (`charge-capture`, CG-01 to CG-09).** A charge line is a priced, payer-agnostic statement that a service was given. Its codes come from the EHR's final, coded facts, or from staff where there are none, and a named human biller confirms them before the charge is final. openPractice never infers a code (ruling 116). Corrections are reversals and replacements.
- **The money ledger (`money-subledger`, SL-01 to SL-12).** Every movement of money is one entry that moves an amount between two of a fixed list of balance buckets, so the ledger always balances, and patient balances belong to the guarantor account (ruling 124). Amounts are kept in whole cents under the foundation's money rule (ruling 103). Entries are never edited; corrections reverse them.
- **Reason codes (`reason-taxonomy`, RT-01 to RT-08).** A fixed list the product ships, on two axes: the kind of money movement and the service category (ruling 125). Each kind maps to one bucket pair and one neutral accounting label. This change confirms that the accounting contract lacks a service-category field and proposes the amendment.
- **The revenue estimate (`revenue-estimate`, RE-01 to RE-09).** Every final charge line carries an estimate of what the practice expects to be entitled to, with its estimator version, cohort and evidence. The full record is built now, with a simple first method that the practice's accountant approves before accounting goes live (ruling 123). True-ups are new records.
- **Good Faith Estimates (`good-faith-estimate`, GF-01 to GF-12).** Single-provider estimates for scheduled self-pay care: issue, replace, deliver with proof, retain, watch the variance and handle a dispute (ruling 121). The legal triggers, contents and thresholds come from a parameter set that counsel verifies; until then openPractice issues none and raises work instead.
- **Patient balances and statements (`patient-balance-and-statements`, PS-01 to PS-11).** The guarantor account's balance, traceable to each visit and charge line; frozen, printable statements (ruling 122); statement terms that set due dates; and suppression of disputed money.
- **Patient payments and refunds (`patient-payments-and-refunds`, PY-01 to PY-11).** Record desk payments, receipts, allocations and credits; generate the deposit slip (ruling 130); keep refunds human-only (ruling 122). The payment processor and the pay link come in the last feature.
- **Eligibility and authorization evidence (`coverage-eligibility-evidence`, EV-01 to EV-06).** Recorded by hand as evidence on a coverage version; electronic checks arrive with the clearinghouse (ruling 127).
- **The participation hold (`participation-gate`, EG-01 to EG-07).** A claim whose rendering provider is not yet enrolled with the payer is held, never rejected, and released in one action once enrolled (ruling 128).
- **Claims (`claim-lifecycle`, CL-01 to CL-13).** A claim per visit per payer, built from final charge lines, scrubbed, released in batches by a named signer (ruling 119), frozen as submitted, and corrected only by replacement or void. Submission goes through the retention change's authorization and outbox.
- **The claim pathway (`claim-pathway-adapter`, CP-01 to CP-09).** One adapter interface that keeps payer and Medicare specifics out of the core: a simulated payer and a manual path that records a claim sent outside the system first, the live clearinghouse later (ruling 118). One Frappe app with adapters as separate packages (ruling 117).
- **Remittances (`remittance-posting`, RM-01 to RM-11).** Electronic and paper payer answers produce the same records. Exactly matched lines post by themselves, with notice and undo; anything else is a proposal for a human (ruling 120). Payer-level adjustments, recoupments and overpayments have a home.

## The six features, in order (ruling 112)

This change hands off to six Speckit features in this order, as ruling 1 allowed for the foundation change. `tasks.md` §2 states each feature's capabilities, gates and acceptance evidence, and how the foundation change's four features slot in between.

1. **Foundations:** the visit thread, the money ledger, reason codes and the revenue-estimate record.
2. **Self-pay visit, end to end** (the first buildable slice, ruling 114): the catalog and prices, minimal booking, manual charge entry, the Good Faith Estimate, desk payments, receipts, the deposit slip and a simple statement.
3. **Claims against a simulated payer**, with the participation hold and eligibility evidence, and the foundation change's clinical-fact consumption folded in.
4. **Remittance posting**, and full patient balances, credits and refunds.
5. **The live clearinghouse** and the Medicare adapter.
6. **The payment processor** and the pay link.

## Dependencies

- **On `main`:** all five merged changes, each draft: `add-reimbursement-retention-control`, `define-mvp-practice-foundation`, `define-customer-financial-data-boundaries`, `define-actors-agents-and-work-inbox` and `define-patient-gateway-and-escalation`.
- **Draft PR opensoft/openPractice-spec#7** (branch `change/follow-up-ruling-93-contract-gaps`, read at head `e746f58`, not merged): EB-13 and EB-15 as it amends them (confirmed diagnoses only, and retractions with a reason code that stop facts being billable and hold unfinalized charges).
- **Draft PR opensoft/openPractice-spec#8**, the corrections change stacked on PR #7 (branch `change/critique-2026-11-15-corrections`, first read at head `4bce39a`, re-checked at head `050bdfd`, still being written, not merged): PR-15, one party per person, and its design call F-7, the one-person alternative for a staff member acting on their own record; BAB-18, one money format with whole cents in storage and one rounding rule, and its design calls F-8 to F-10; and the agent rules it amends in AM-01, AO-04 and AO-07.
- **Draft PR opensoft/openPractice-spec#10** (branch `change/define-practice-readiness`, first read at head `e9748d7`, re-checked at head `d4724e1`, not merged): SR-02 and SR-04, and its design call RD-11 on who counts as the practice's accountant. Its finding that the roster has no actor type or recipe for an accountant (its I-1 and RI-51) blocks every accountant sign-off this change needs until it is settled.

This change cites requirements of open pull requests by ID and describes them. It never quotes their text. It should merge after them.

## Non-goals

- **Clinical content of any kind.** The EHR owns it (Principle IV, PR-01, EB-13, EB-16).
- **The books.** No general ledger, chart of accounts, bank balance, bank reconciliation or tax (BAB-02). Posting mechanics stay with `openpractice-accounting-integration/1.0` (ruling 26).
- **Deposit explanation and the bank register.** They stay with BAB-09 to BAB-14 and AI-17. This change supplies the payments, receipts, trace numbers and deposit slips they explain.
- **Memberships, subscriptions, retail and sales tax** (rulings 24 and 26).
- **Appointment reminders, a waitlist and self-scheduling** (ruling 115); appointment or payment operations on the gateway or EHR 1.0 contracts (PG-12, EB-19).
- **The cash-drawer count and the day's close sheet** (ruling 130), phone and fax intake, and referrals.
- **Commercial payers and secondary claims as built features** (ruling 113); the data shape allows both.
- **Loaded payer fee schedules and contract import** (ruling 126); the learned payer baseline, the drift alarm, appeal packets, payment plans, collection agencies, workers' compensation and auto claims, non-visit revenue and patient financing.
- **The claim-pathway, remittance and payment-processor contracts themselves.** Each is defined by a governed amendment of this change before the feature that needs it starts (design call CR-24).
- **Any legal, accounting or billing rule.** Where one matters, this change names the person who decides it and fails closed until they do.
- **Promoting anything to canonical specs** (ruling 109), editing another change, runtime code, and other repositories.

## Capabilities

### New Capabilities
- `encounter-thread` (TH-01 to TH-16): one thread per visit-intent and per enumerated case; stations, conditions, clock, amount and owner; dated, attributed transitions that never rewind; links; re-association; the versioned thread contract; work items; agent ceilings; no clinical content or PHI in logs.
- `administrative-appointment` (AP-01 to AP-09): booking, rescheduling, cancelling, no-shows and walk-ins as thread moves; capacity from the roster; the payment-hold clause; the MVP's limits.
- `service-catalog-and-pricing` (SC-01 to SC-08): catalog items, licensed code overlays, one price list by date of service, one self-pay discount, service categories, offering-route facts, no payer fee schedules.
- `charge-capture` (CG-01 to CG-09): payer-agnostic charge lines from EHR facts or staff entry, biller confirmation, finalization checks, duplicate guard, waiting on documentation, corrections, and the entries a final charge posts.
- `money-subledger` (SL-01 to SL-12): balanced two-bucket entries, the fixed bucket list, entry content, whole cents, reversals and as-of balances, counterparty accounts, allocations, derived balances, reconciliation totals, disputes, old-system balances, audit.
- `reason-taxonomy` (RT-01 to RT-08): the shipped two-axis list, bucket pairs and neutral labels, service categories, what reaches accounting, remittance codes, discount versus write-off, concession versus credit loss, versioning.
- `revenue-estimate` (RE-01 to RE-09): estimate records, accountant-approved versions, the first method, conservative estimates, true-ups, close re-estimates, disputes, back-testing, the D8 record.
- `good-faith-estimate` (GF-01 to GF-12): scope, requirement test, counsel-verified parameters, clock, content, replacement, delivery, retention, variance, disputes, late coverage changes, governance.
- `patient-balance-and-statements` (PS-01 to PS-11): guarantor-account balances, traceability, frozen and printable statements, terms, communication restrictions, dispute suppression, holds, events, sending, and the material write-off amount.
- `patient-payments-and-refunds` (PY-01 to PY-11): payment recording, receipts, prepayments and credits, allocation, the deposit slip, credit-balance cases, refunds, own-record refunds, chargebacks, the processor and pay link, holds.
- `coverage-eligibility-evidence` (EV-01 to EV-06): manual eligibility and authorization evidence, electronic checks later, readiness, coverage changes, frozen evidence.
- `participation-gate` (EG-01 to EG-07): the participation record, one check at two moments, hold not reject, one-action release, the remainder, the deadline escape, deposit enrollment.
- `claim-lifecycle` (CL-01 to CL-13): claim grain, payer order, building, scrub, explicit batch release, the deadline escape, frozen submissions, replacements and voids, statuses, filing clocks, protected submission, accounting effect, holds.
- `claim-pathway-adapter` (CP-01 to CP-09): one interface, jurisdiction isolation, the simulated payer, the manual path, the live clearinghouse, the Medicare adapter, licensed formats, isolation, tests.
- `remittance-posting` (RM-01 to RM-11): remittance versus payment, paper and electronic intake, re-association, automatic exact posting, proposals, service-line content, posting entries, payer-level adjustments, recoupments, reversals, protected access.

### Modified Capabilities

None. This change edits no other change. Where it needs another change altered, `design.md` lists the amendment under "Proposed amendments" for that change's owner.

## Impact

- **New openPractice data:** threads, transitions and links; appointments; catalog items, price lists and the self-pay discount; charge sets and lines; ledger entries and allocations; estimates, estimator versions and close snapshots; Good Faith Estimates and their parameter sets; statements, receipts, payments, refunds and deposit slips; eligibility and authorization evidence; participation records and holds; claims, claim versions, batches and status events; remittances and their lines.
- **One new contract:** `openpractice-thread/1.0`, with its canonical schema in `contracts/` and `contracts/SHA256SUMS`. The claim-pathway, remittance and payment-processor contracts come later (CR-24).
- **Proposed amendments to other changes** (`design.md`): the service-category field in the accounting contract (ruling 125), and conforming entries for the work inbox, the foundation's task gates, the customer-financial offering rule and the readiness inventory.
- **PHI exposure:** every record here is PHI or money and sits behind openPractice's access controls. Nothing new goes to the accounting system beyond the service category already in AI-15's summary, which carries no personal data. Each feature owes a security review and a risk-analysis entry (Principle II).
- **Contract tests:** the thread contract, and each later adapter contract, against synthetic fixtures only.
- **Runtime:** none. No runtime code, dependency or vendor claim is added.
