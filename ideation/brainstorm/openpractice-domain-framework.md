# openPractice Domain Framework — Proposal

**Status:** Proposal for Brett Heap's review. Nothing here is ratified. Synthetic examples only; no real patient, payer or financial data.
**Request addressed:** "analyze how we will break down the clinical vs commerce vs customer vs accounting vs billing etc. lets try to create the framework for our system."
**Nature:** conceptual architecture and responsibility analysis. It is not an implementation plan, a repo change, or Medicare compliance research.

---

## 0. Overview and bottom line

**Recommendation in one paragraph.** Treat openPractice as a *practice-administration and revenue-cycle system* that holds a clear line on both sides. On one side, it never holds clinical truth: openChart (or another EHR) does, and openPractice only references it by ID, version and provenance. On the other side, it never holds the general ledger: MSBC or ERPNext does, and openPractice sends it balanced, idempotent postings. In between, openPractice owns the **operational receivable**: charges, claims, remittances, patient balances and payments. The most important choice is that **openPractice is the subledger of record for receivables, and the ERP is the ledger of record for money in the books**. Choosing this avoids double AR, avoids duplicate invoices, and keeps the ERP out of clinical and payer detail.

**Key proposals (all marked "proposed" until Brett rules):**

1. Nine bounded contexts (Section 2); for the MVP, **merge several into one thin "Revenue Cycle core"** and keep commerce, scheduling and staff minimal.
2. **One identity per human**: a single `Party`, with *roles* (patient, guarantor, subscriber, staff, provider). "Customer" is **not** a role of a patient; it is an accounting-side artefact only if needed.
3. Hard distinctions between encounter, appointment, charge, claim, patient invoice/statement, payment, and ledger posting (Section 4). They are different objects with different owners.
4. **No per-patient ERP Customer and no ERP Sales Invoice for claims by default.** Post summarized journal-style entries to payer-class control accounts; keep patient-level detail in openPractice (Section 5).
5. Jurisdiction-specific Medicare logic sits behind a **Claim Pathway Adapter** so MVP is not blocked on Medicare jurisdiction (Section 8).
6. **Revenue recognition (Section 5A):** net revenue is the constrained expected entitlement estimated at service and updated at each close; billing, cash and disputes are evidence events, not revenue triggers. This corrects the first version of this document.
7. Eight decisions for Brett, each with a recommended default (Section 10).

**Do not conflate three different kinds of "boundary".** This document is about **domain boundaries** (who owns which fact). It does not decide **repository layout** (already set: assembly `opensoft/openPractice`, spec `opensoft/openPractice-spec`, code `opensoft/openPractice-code`) nor **deployment/service boundaries** (how many Frappe apps/sites/services run). A domain boundary may live as a module inside one Frappe app. See Section 2.4.

---

## 1. What is established vs proposed vs open

| Status | Item | Source |
|---|---|---|
| **Established** | openPractice owns registration, scheduling, coverage, authorizations, charge capture, claims, remittance, patient billing, payments, operational tasks, practice reporting | Project README |
| **Established** | openPractice does not own signed clinical facts, encounters, medication lists, results, orders; openChart or another EHR does | README, Brett |
| **Established** | Accounting is provided by MSBC (Business Central) or ERPNext | Brett, DECISIONS.md |
| **Established** | MVP = minimum practice management needed to bill Medicare for a patient; broader scheduling/office/staff only as needed | DECISIONS.md (2026-11-14) |
| **Established** | Original Frappe-native app (`open_practice`), not an OpenEMR/Marley fork; usable standalone with openChart or another EHR | README |
| **Established** | Repo is at governance-scaffold stage; no application or data model built | README |
| **Established** | MedxFactory may automate operations only through bounded, capability-aware integration contracts | README |
| **Proposed** | Everything in Sections 2–10, including the revenue-recognition proposal in Section 5A | This document |
| **Established** | The ERP receives only benign service categories (office visit, lab work, lab test); openPractice keeps exact billing codes, the billing/claim file and the payer correspondence ledger | Brett (accounting-boundaries note) |
| **Open** | Final revenue-recognition policy, reporting basis and estimator parameters; need the practice's accountant | Section 5A.4 |
| **Open** | Medicare jurisdiction and claim pathway (isolated behind an adapter, not blocking) | Section 8 |
| **Open** | Which ERP is used first; whether Frappe site co-hosts ERPNext | Decisions D3, D4 |

---

## 2. Bounded-context map

### 2.1 The nine contexts

| # | Context | One-line purpose | Owner |
|---|---|---|---|
| C1 | **Clinical** | Signed clinical facts: encounters, orders, meds, results, diagnoses/procedures as documented | **openChart / other EHR** (external to openPractice) |
| C2 | **Party, Identity & Relationships** | Who people and organizations are; their roles and relationships (guarantor, subscriber) | openPractice |
| C3 | **Practice Operations** | Appointments, schedule, registration workflow, tasks, staff work queues | openPractice |
| C4 | **Commerce, Catalog & Pricing** | Service catalog, fee schedule, price lists, self-pay and package pricing | openPractice |
| C5 | **Coverage, Payer & Authorization** | Insurance policies, eligibility checks, payer rules, prior authorizations | openPractice |
| C6 | **Billing & Revenue Cycle** | Charge capture, claim lifecycle, denials, patient balance, statements | openPractice |
| C7 | **Payments, Remittance & Reconciliation** | Payer remittances, patient payments, allocation, settlement, bank/deposit matching | openPractice (operational); bank truth stays with accounting |
| C8 | **Accounting** | General ledger, chart of accounts, AP, bank, financial statements, tax | **MSBC or ERPNext** (external to openPractice) |
| C9 | **Cross-cutting: Identity/Access, Audit, Integration** | Authentication, authorization, audit/provenance, integration contracts | Platform concern inside openPractice, shared by all |

### 2.2 Text diagram

```
        ┌────────────────────────┐                 ┌─────────────────────────┐
        │ C1 CLINICAL            │                 │ C8 ACCOUNTING           │
        │ openChart / other EHR  │                 │ MSBC *or* ERPNext       │
        │ signed facts           │                 │ general ledger          │
        └───────────┬────────────┘                 └────────────▲────────────┘
   reference-only   │  (IDs, versions,                          │ balanced,
   evidence events  │   provenance)                             │ idempotent postings
 ┌──────────────────▼───────────────────────────────────────────┴──────────────┐
 │                        openPractice                                        │
 │  C2 Party/Identity ── C3 Practice Ops ── C4 Catalog/Pricing                │
 │          │                  │                  │                           │
 │          └──────── C5 Coverage/Payer/Auth ─────┤                           │
 │                              │                 │                           │
 │                     C6 Billing / Revenue Cycle ◄┘                          │
 │                              │                                             │
 │                     C7 Payments / Remittance / Reconciliation              │
 │                                                                            │
 │  C9 Identity/Access · Audit/Provenance · Integration (applies to all)      │
 └──────────────────────────┬─────────────────────────────────────────────────┘
                            │ Claim Pathway Adapter (jurisdiction-specific)
                            ▼
                 Medicare claim channel / payer responses
```

### 2.3 MVP treatment: merge, minimise, defer

| Context | MVP treatment | Why |
|---|---|---|
| C1 Clinical | **Not built.** Consume evidence events/references only | Out of scope by established boundary |
| C2 Party | **Build, small.** One Party with patient, guarantor, subscriber roles | Needed to bill anyone; identity mistakes are expensive to fix later |
| C3 Operations | **Minimal.** Registration workflow and a work queue for exceptions. Full scheduling only if the EHR cannot supply appointment/encounter reference | MVP is billing-first (DECISIONS.md) |
| C4 Commerce/Pricing | **Minimal.** A service/procedure catalog with one fee schedule. No packages, memberships, retail or inventory | Needed for charge amounts, nothing more |
| C5 Coverage | **Build, narrow.** Policy record, Medicare coverage record, eligibility result stored as evidence; authorization as optional, conditional record | Required to bill; depth depends on pathway |
| C6 + C7 | **Merge into one "Revenue Cycle core" for MVP** (separate modules internally, one release unit) | Charges, claims, remittance and balances share one lifecycle; splitting early only adds integration seams |
| C8 Accounting | **Integrate, don't build.** Posting contract and reconciliation | Established boundary |
| C9 Cross-cutting | **Build first, thin.** Roles, audit trail, idempotency keys, integration envelope | Everything else depends on it |

**Merge candidates:** C6 and C7 for MVP (above). **Keep apart even in MVP:** C1/C8 (external) and the Claim Pathway Adapter (so jurisdiction change never touches core).
**Keep minimal:** C3 and C4.

### 2.4 Domain vs repository vs deployment (kept separate)

| Question | Answered by | Current state |
|---|---|---|
| Who owns this fact? | **Domain boundary** (this document) | Proposed here |
| Where do spec and code live? | **Repository layout** (assembly/spec/code legs) | Established |
| How many apps, sites, services run? | **Deployment boundary** | Open; recommended default: one Frappe app `open_practice` with internal modules named after the contexts, so domain lines stay enforceable without extra services |

---

## 3. Ownership table

### 3.1 Responsibilities and records

| Context | Responsibilities | Canonical entities | System of record | Interfaces | Exclusions |
|---|---|---|---|---|---|
| C1 Clinical | Document care; sign notes; orders, results; diagnoses/procedures as documented | Encounter, Order, Medication, Result, Clinical consent, Signed note | openChart / EHR | Emits evidence references to openPractice | Pricing, coverage, balances |
| C2 Party | One identity per human/org; roles; relationships; contact; financial responsibility links | Party, PartyRole, Relationship, Address/Contact | openPractice (patient *demographic and administrative* copy); EHR is clinical master of the patient | Matches to EHR patient ID via cross-reference | Clinical history; accounting customer |
| C3 Operations | Appointments, registration steps, tasks, staff queues | Appointment, RegistrationCase, WorkItem, Location/Resource | openPractice | Appointment ↔ encounter reference with EHR | Clinical encounter content |
| C4 Commerce | What can be charged and at what price | ServiceItem, FeeSchedule, FeeScheduleEntry, (later) Package | openPractice | Charge capture reads price; GL account mapping reads item | Payer-allowed amounts (those are contract data in C5) |
| C5 Coverage | Insurance facts, eligibility, authorization | CoverageRecord, Payer, EligibilityCheck, Authorization, Payer rule reference | openPractice | Claim Pathway Adapter; eligibility channel | Payment posting |
| C6 Billing | Turn documented services into money owed | Charge, Claim, ClaimLine, ClaimStatusEvent, PatientBalance, PatientStatement | openPractice | EHR evidence in; Adapter out; Accounting posting out | Clinical content; GL |
| C7 Payments | Record money received and allocate it | Remittance, RemittanceLine, Adjustment, PatientPayment, Allocation, Settlement/Deposit | openPractice (operational facts); accounting holds bank/cash truth | Adapter in (remittance); processor/bank via accounting | Setting clinical or coding facts |
| C8 Accounting | Ledger, periods, bank, financial reporting, tax | Account, JournalEntry/GL entry, Period, BankAccount | MSBC or ERPNext | Receives postings; returns posting IDs and reconciliation status | Patient/claim detail, clinical data |
| C9 Cross-cutting | Who may do what; who did what; how systems talk | User, Role, Permission grant, AuditEvent, IntegrationMessage | openPractice platform | All contexts | — |

### 3.2 Roles without duplicate identities

Principle: **one `Party` per real person or organization; everything else is a role or a link, never a second person record.**

| Term | What it is | Modelled as | Notes |
|---|---|---|---|
| Person | A human | `Party` (type person) | The only identity record |
| Organization | Payer, employer, practice | `Party` (type organization) | Same mechanism |
| Patient | A person receiving care | `PartyRole: patient` + cross-reference to the EHR patient ID | EHR is the clinical master; openPractice keeps the administrative copy |
| Guarantor | Person financially responsible for a balance | `PartyRole: guarantor` + link to patient(s) | May be the patient themself (same Party) |
| Subscriber / policyholder | Person who holds the insurance policy | `PartyRole: subscriber` on a CoverageRecord | May be the patient (self) or another Party |
| Payer | Organization paying claims | `Party` (org) + `Payer` profile | Medicare is a Payer |
| Provider | Clinician rendering/billing | `PartyRole: provider` + provider identifiers (NPI etc.) | Matches EHR clinician reference |
| Staff | Employee/user of the practice | `PartyRole: staff` linked to a `User` | Staff and provider may be the same Party |
| **Customer** | An *accounting* concept: who a sales invoice is billed to | **Not a role of patient.** If the ERP needs one, it is a payer-class or "patients (control)" party in the ERP | Equating patient with customer is the main trap; see 5.3 |

**Consents.** Keep two families separate. *Clinical consents* (treatment, procedures, release of clinical information) are owned by the EHR. *Financial/administrative consents* (assignment of benefits, financial responsibility acknowledgement, authorization to bill, payment-method or statement preferences, communication preferences) are owned by openPractice in C2/C5. A financial record may *reference* a clinical consent but must not store or alter it.

---

## 4. Precise distinctions

### 4.1 The chain of objects

| Object | What it is | Owner | Created by | Not the same as |
|---|---|---|---|---|
| **Appointment** | Planned time slot for a person/resource | openPractice C3 | Scheduling | A clinical encounter; can occur with no care, or care can occur with no appointment (walk-in) |
| **Clinical encounter** | Documented care event | openChart C1 | Clinician | An appointment; a charge |
| **Billable service evidence** | Signed statement that a service was performed, with provider/date/(diagnosis/procedure references) | openChart C1, delivered as evidence event | EHR on signing | A charge (no price, no payer) |
| **Charge** | A priced, payer-agnostic line: "this service, this provider, this date, this amount" | openPractice C6 | Charge capture from evidence | Claim; invoice |
| **Claim (and claim line)** | A request to a payer for payment for charges, shaped by the payer pathway | openPractice C6 | Claim builder | Patient invoice; the charge itself |
| **Patient invoice / statement** | Request to the responsible *person* for their share (after adjudication, or for self-pay) | openPractice C6 | Patient billing | A claim; ERP sales invoice |
| **Payment** | Money actually received (from payer or patient) | openPractice C7 (fact); bank in C8 | Remittance/receipt | An adjustment; an allocation |
| **Ledger posting** | Accounting entry reflecting financial effect | Accounting C8 | Posting contract | A claim or invoice; clinical truth |

Rule: **each arrow in this chain is a new object with its own ID that references the prior one. Nothing is overwritten; corrections are new objects.**

### 4.2 Payer outcomes

| Term | Meaning | Effect |
|---|---|---|
| **Rejection** | Claim failed front-end validation (format, identifiers, missing data); it was *never adjudicated* | Fix and resubmit; no financial effect; claim stays "open" |
| **Denial** | Claim was adjudicated and payment refused in whole or part, with reason codes | Appeal, correct and resubmit, or write off per authority; financial effect recorded |
| **Allowed amount** | What the payer's contract/rules allow | Basis for expected payment |
| **Contractual adjustment** | Difference between billed charge and allowed amount that nobody may bill the patient for | Reduces receivable; accounting posting (contra-revenue/adjustment) |
| **Patient responsibility** | Part the person owes (deductible, coinsurance, copay, non-covered, as reported by payer) | Becomes patient balance |
| **Write-off** | A *management decision* to stop collecting | Needs explicit authority; separate from contractual adjustment |
| **Allocation** | Assigning a received payment to specific charges/claim lines/balances | Operational; changes balances |
| **Settlement** | Closing a payment/remittance against the actual deposit/bank movement | Reconciliation; proves cash matches |

### 4.3 Things never to equate

- **Patient ≠ customer.** Patients are care recipients; billing may go to a guarantor, a payer, or both.
- **Invoice ≠ claim.** A claim goes to a payer under payer rules; an invoice goes to a person; one charge can feed both.
- **ERP ledger ≠ clinical truth.** The ledger records money; it must not drive or store clinical content.
- **Remittance ≠ deposit.** An electronic remittance advice can arrive before or after the money, and one deposit can cover many remittances.
- **Adjustment ≠ write-off** (contractual versus decision).

---

## 5. Source of truth, subledger ownership and accounting contract

### 5.1 Source-of-truth proposal

| Fact | System of record | Others hold |
|---|---|---|
| Signed clinical content, encounter, orders, results | openChart / EHR | Reference ID, version, hash, provenance only |
| Patient demographics (clinical master) | EHR | openPractice keeps administrative copy with cross-reference |
| Appointment | openPractice | EHR keeps reference if needed |
| Coverage, eligibility result, authorization | openPractice | — |
| Charge, claim, claim status | openPractice | Accounting sees posted summaries only |
| Remittance, allocation, patient balance | openPractice | Accounting sees posted summaries |
| Revenue estimates, estimator versions, close snapshots, exact billing codes, claim/billing file, payer correspondence ledger | openPractice | Accounting sees category totals and a batch reference only |
| Cash, bank, GL balances, periods | Accounting | openPractice sees posting IDs and reconciliation status |
| Audit of actions | Each system for its own; openPractice for its own workflows | — |

### 5.2 Ownership alternatives for receivables

| Option | Description | Pros | Cons | Verdict |
|---|---|---|---|---|
| **A. openPractice subledger, ERP summary GL (recommended)** | openPractice holds patient/payer receivable detail; sends balanced summary postings to payer-class control accounts; ERP never sees patient-level AR | One AR; ERP stays small; no clinical/PHI-like detail in ERP; works for MSBC and ERPNext identically | Requires a reconciliation (subledger total = control account) and a posting contract | **Proposed default** |
| B. ERP is AR master; openPractice mirrors | Create ERP sales invoices/customers per patient or claim | Uses ERP aging/dunning out of the box | Dual AR, claim lifecycle does not fit invoice lifecycle, patient identities spread into ERP, hard to remain ERP-neutral (MSBC vs ERPNext differ) | Reject for MVP |
| C. Hybrid: patient statements as ERP invoices, claims in openPractice | Claims in openPractice; patient balances in ERP | Reuses ERP collections for patients | Splits one patient balance across systems; payer-adjudication determines patient share, so ordering and reversal become fragile | Defer; revisit only if the practice wants ERP-side dunning |

### 5.3 Do not assume ERPNext/Frappe features are free modules

Because openPractice is Frappe-native, ERPNext's doctypes (Customer, Sales Invoice, Payment Entry, Item, Price List) look tempting. Evaluate them per feature, not as a bundle:

| Facility | Tempting use | Recommendation |
|---|---|---|
| ERPNext Customer | Patient as customer | **Do not.** Creates a second competing identity. Use at most a few control-party records (e.g. "Medicare receivable control") |
| ERPNext Sales Invoice | Claim or patient statement | **Do not for claims.** Would duplicate AR. Patient statements stay in openPractice |
| ERPNext Payment Entry | Posting receipts | Use only as the *accounting posting vehicle* at summarized level, if ERPNext is chosen; otherwise Journal Entry |
| ERPNext Item / Price List | Service catalog | Keep catalog in openPractice (C4) with a mapping to GL account; do not require ERPNext to be installed |
| ERPNext Healthcare/Marley doctypes | Clinical or billing bases | **Excluded**: original implementation, not a Marley fork |
| Frappe framework itself (permissions, workflow, audit/versioning, REST, background jobs) | Platform | Use: this is the native base |

Also: **openPractice must work with MSBC (no Frappe) as accounting**, so it cannot depend on ERPNext doctypes being present. Treat the ERP as an *adapter target*: one adapter for ERPNext, one for MSBC. **Assume only one ERP is active per deployment**, never both.

### 5.4 Accounting posting contract (proposal)

openPractice emits **posting batches**, not invoices.

| Event in openPractice | Posting concept (summarized) | Notes |
|---|---|---|
| Service performed, charge captured | Dr Unbilled AR (gross), Cr Service revenue by benign category; Dr Revenue deduction, Cr Gross-to-net allowance. Net revenue = constrained expected entitlement | **Corrected:** revenue is not list price. Gross presentation is allowed only with its deduction posted at the same time. See Section 5A |
| Claim submitted | Dr Billed AR, Cr Unbilled AR | No revenue effect |
| Adjudication received | Realize contractual adjustment against the allowance; true-up any estimate difference through revenue deduction | Revenue changes only by the difference between estimate and evidence |
| Remittance posted (payer payment) | Dr Cash clearing, Cr Billed AR (payer) | Cash clearing is cleared by bank reconciliation; no revenue effect |
| Patient responsibility transfer | Dr Patient AR, Cr Payer AR | Moves between control accounts; no new revenue |
| Patient payment | Dr Cash clearing, Cr Patient AR | No revenue effect |
| Dispute opened or resolved | Hold or release the disputed amount in the gross-to-net allowance through revenue deduction | Dispute status is operational, not an asset (Section 5A.2.6) |
| Credit loss (authorized) | Dr Credit-loss expense, Cr Credit-loss allowance or AR | Only for amounts the practice expected to collect; requires named approver |
| Overpayment or recoupment | Dr Revenue deduction (if a price change), Cr Refund liability | Liability from identification |
| Refund / reversal | Opposite entry referencing the original posting | Never edit or delete the original; named approver |

Posting lines carry **benign service categories only** (for example office visit, lab work, lab test), versioned and effective-dated, plus `accounting_date`, `effective_date` and estimator version; no patient identifiers, diagnosis or procedure codes, payer correspondence or claim content.

Contract rules:
- Postings are **summarized by period/payer class/service category/account**, with a **detail manifest** held in openPractice (ID list, amounts) so any ledger line can be traced back.
- Each batch carries a stable **posting batch ID, version and idempotency key**; resending produces the same result, not a duplicate.
- Batches **must balance** to the minor unit in a single currency; the ERP rejects unbalanced batches.
- The ERP returns its own **document ID** and status; openPractice stores it.
- **Reconciliation:** (a) subledger total per control account equals ERP control-account balance as of the same cut-off; (b) cash clearing equals bank-matched deposits; (c) differences open a reconciliation work item, never an automatic fix.

### 5.5 Failure modes this design deliberately blocks

| Risk | Control |
|---|---|
| Double AR | Only openPractice holds receivables detail; ERP sees control totals |
| Duplicate invoices/postings | Idempotency key per batch; no invoice objects created in ERP |
| Unsupported payer features | Payer capabilities declared in the adapter; features not declared are not offered |
| Both ERPs at once | One accounting adapter active per deployment |
| Clinical data leakage into ERP | Posting carries IDs/amounts, never diagnoses or notes |

---

## 5A. Revenue recognition: problem statement and proposed solution

> **Status: proposal, not ratified.** This section corrects the revenue-recognition guidance in the first version of this document (old 5.4 and D5; see 5A.1.5). The final policy needs approval by the practice's accountant. Amounts are synthetic. Research for this section was done on 2026-11-14; sources and what was verified directly are listed in 5A.5.

### 5A.1 Problem statement

#### 5A.1.1 The question

Brett's question, in short: a practice bills first the full price of a service, then a discounted or contracted amount depending on the insurance, then receives payment, then may dispute part of it, and ends with a final amount after about 90 days. The accounting system must see only benign categories (office visit, lab work, lab test) while openPractice keeps the exact billing codes, the claim/billing file and the payer correspondence ledger. **At which points, and in what amounts, is revenue recognized, so that the books close on time without waiting for final settlement?**

#### 5A.1.2 The amounts involved (definitions)

| Term | Meaning | Known when | Is it revenue by itself? |
|---|---|---|---|
| Full / list / gross amount | Practice's standard charge for the service (charge master) | Day 1 | **No.** An operational figure; never automatic revenue |
| Submitted / billed amount | Amount put on the claim; usually the full amount; **not** necessarily what the payer will allow | Day 10 | **No.** Billing is an administrative step |
| Contracted / allowed estimate | What the fee schedule or contract suggests the payer will allow, before any claim exists | Day 1 (estimate) | Starting point only |
| Adjudicated allowed amount | Allowed amount the payer actually decides | Day 60 | Evidence; updates the estimate |
| Payer responsibility / patient responsibility | Split of the allowed amount between payer and patient (deductible, coinsurance, copay) | Estimate day 1; actual day 60 | Parts of the same entitlement; transferring between them creates no revenue |
| Cash received | Money that arrived (remittance, deposit, patient payment) | Day 60 onward | **No.** Settles a receivable; does not create revenue |
| Disputed / appealed amount | Part the practice contests (denial, short-pay, audit) | Day 60 to day 90+ | **No.** An operational status, not an asset |
| Expected net collectible consideration | Practice's estimate of what it will be entitled to, **constrained** so a significant reversal is not probable | Every close | **Yes. This is net revenue** |
| Final entitlement | Amount actually settled after disputes | Day 90+ | Where cumulative revenue ends up |

Two traps in these terms. First, not every difference between gross and cash is bad debt: discounts and expected short-pays are price concessions (reduce revenue), while a later failure to pay an amount the practice truly expected to receive is a credit loss (expense). Second, not every write-off is a revenue reduction: the same write-off may be a use of an existing allowance, a revenue adjustment or a credit-loss expense, depending on why and when.

#### 5A.1.3 What is known at each point in time

| Day | Event | Information gained | Natural trap |
|---|---|---|---|
| 1 | Service performed, charge captured | List price; fee-schedule or contract expectation; payer/patient split estimate | Booking list price as revenue |
| 10 | Claim submitted | Billed amount; acceptance or rejection | Treating "billed" as a revenue event |
| 60 | Remittance, payment, denial | Adjudicated allowed; payer paid; patient share; part denied and disputed | Waiting for cash to book revenue; or booking the disputed part as if certain |
| 90 | Appeal result, final settlement | Final entitlement | Reopening closed months, or never fixing them |

Books must close monthly (or on the practice's cycle). Between day 1 and day 90 at least one period usually closes, so each close needs a defensible estimate that does not wait for later days.

#### 5A.1.4 Why simple approaches fail

| Approach | What goes wrong |
|---|---|
| Revenue at gross charge, adjustment at adjudication | Revenue and AR overstated at every close until adjudication; list price is not what the practice expects to be entitled to (this was the first version's recommendation; corrected below) |
| Revenue at billing | Same overstatement; billing is not a performance event |
| Revenue at cash | Delays revenue past service; service already performed; accrual statements understated |
| Revenue at final settlement (day 90+) | Cannot close timely; violates the requirement to estimate and update each period |
| Treat every shortfall as bad debt | Mixes price concessions (revenue) with credit loss (expense); distorts revenue and metrics |
| Record disputed amount at full face value | Overstates revenue; a dispute outcome is uncertain |
| Send claim detail to the ERP | Breaches the requirement that the ERP sees only benign categories; duplicates the receivable subledger |

#### 5A.1.5 Correction to the first version (old 5.4 and D5)

The first version recommended "gross at charge plus contractual adjustment on adjudication". **That is withdrawn as a revenue policy.** Gross charges may exist as an operational figure and may appear as a gross line with a contra deduction (see 5A.3), but reported **net** revenue must reflect the appropriately constrained expected entitlement **at the time the service is performed**, not list price held until a remittance arrives. Contractual adjustments and expected concessions belong in the period of service, with later changes recognized when they become known.

### 5A.2 Proposed solution

#### 5A.2.1 Principles

1. **Net revenue = constrained expected entitlement**, estimated when the service is performed and updated at each close. Gross charge is a memo or a gross line that is always accompanied by its deduction.
2. **Billing, adjudication, remittance advice, bank cash and dispute resolution are evidence events, not recognition triggers.** Each may update the estimate; none by itself creates revenue.
3. **Two separate allowances:** a transaction-price (gross-to-net) allowance, which reduces revenue, and a credit-loss allowance, which is an expense on a separate line.
4. **No invented precision for disputes:** the disputed part stays out of the recognized asset until it is probable that including it will not cause a significant reversal.
5. **openPractice holds every detail; the ERP holds categories and totals.** Estimates, versions and evidence are retained in openPractice and are reproducible for any close.
6. **Proposals only:** all estimates, approvals, reversals and write-offs are proposed by automation and approved by a named person.

#### 5A.2.2 Reporting basis is not the payer pathway

The **financial reporting basis** (US GAAP accrual, IFRS accrual, or a cash or tax basis for a small practice) is an entity-level choice made with the accountant. It is independent of the **payer submission pathway** (for example the Medicare claim pathway and jurisdiction handled in Section 8). openPractice needs neither answer to design the revenue events below. Differences:

| Basis | Revenue timing | Consequence for openPractice and ERP |
|---|---|---|
| Accrual, US GAAP (ASC 606) | At service, at the constrained expected amount | The design in this section |
| Accrual, IFRS (IFRS 15) | Same model; some threshold and impairment wording differs (5A.2.3) | Same events; accountant selects wording and thresholds |
| Cash or tax basis | When cash is received (tax rules vary) | Book-to-tax reconciliation is done in the ERP from cash and bank data; openPractice supplies cash events it already holds. This is a reporting layer, not a different subledger |

Management or lender reporting that uses accrual figures while tax is on cash basis is a normal combination; the accountant owns the reconciliation.

#### 5A.2.3 How the standards apply (summary)

The model below is ASC 606 (US GAAP) for the practice's patient-service revenue; IFRS 15 follows the same five-step model and, for these points, nearly identical paragraph numbering. Healthcare-specific interpretation comes from the AICPA/ASC 954 community and Big Four/HFMA guidance listed in 5A.5.

| Topic | ASC 606 (US GAAP) | IFRS 15 | Healthcare interpretation | Effect on openPractice |
|---|---|---|---|---|
| Contract exists; collectibility | 606-10-25-1(e): collectibility **probable**, considering ability and intention to pay; consideration may be less than price if a price concession may be offered | Para 9(e) equivalent (not re-verified in this research; see 5A.5) | Emergency or uninsured care may be a contract with an implicit price concession rather than a failed contract | Estimator takes payer class and patient class as cohort inputs |
| Transaction price includes variable consideration | 606-10-32-5 to 32-9: price concessions (32-6), implicit concessions from customary practice or intent (32-7), expected value or most likely amount (32-8), one method (32-9) | Paras 50 to 54 (verified) | Implicit price concession = gross price minus what the provider expects to collect; portfolio approach is common (PwC 3.3.1) | Estimate on a cohort (portfolio) basis, expected-value method |
| Constraint | 606-10-32-11 and 32-12: include only to the extent a significant reversal of cumulative revenue is **probable not to occur**; factors: third-party judgment, long uncertainty, limited experience, many outcomes | Para 56 (verified): "**highly probable**" | Denials, audits, retrospective settlements are variable consideration (PwC 3.3.2) | Disputed amount excluded until constraint is met |
| Update each period | 606-10-32-14; changes in price after inception go to revenue in the period of change for satisfied obligations: 32-42 to 32-43 | Paras 59 and 87 to 90 (not re-verified) | Later changes in estimate flow through transaction price (PwC 3.3.1.2) | Day 60 and day 90 true-ups are revenue adjustments |
| Receivable vs contract asset | 606-10-45-1 to 45-4: receivable is an unconditional right (only time passes); contract asset is conditioned on something else | Paras 105 to 108 (cited from the standard, not re-verified) | Many practices present unbilled amounts as unbilled receivables; classification is a policy for the accountant | Unbilled vs billed AR kept as separate balances |
| Impairment | Credit losses under Topic 326 (CECL) are an expense presented separately from revenue (ASC 606-10-45-3/4 text still references Topic 310; accountant to confirm wording) | IFRS 9 expected credit losses | Credit loss is an expense; implicit price concession reduces revenue (PwC 5.2) | Separate credit-loss allowance; separate reason codes |
| Cash received before service | 606-10-45-2: contract liability; 606-10-25-7 limits recognition when criteria fail | Paras 15 and 106 (not re-verified) | Prepaid deposits and copays | Liability account until service performed |
| Refund liability | 606-10-32-10 (not re-verified) | Para 55 (verified): consideration received or receivable the entity does not expect to be entitled to | Overpayments identified by the practice must be reported and returned (42 CFR 401.305(b)(1), verified) | Overpayment is a liability, not revenue |
| Subsequent events and errors | ASC 855-10-25-1 and 25-3; ASC 250-10-45-17 and 45-23 | IAS 10; IAS 8 | A settled amount that confirms a condition at close is a recognized subsequent event | Late events handled in 5A.2.8 |

#### 5A.2.4 Events and their accounting effect

| Event (openPractice) | Revenue effect | Accounting entry (category level) | Classification rule |
|---|---|---|---|
| Service performed, charge captured | **Yes**: constrained expected net amount (gross line plus gross-to-net deduction if gross presentation is used) | Dr Unbilled AR (gross), Cr Service revenue by category; Dr Revenue deduction, Cr Gross-to-net allowance | Unbilled until the claim is submitted |
| Claim submitted | None | Dr Billed AR, Cr Unbilled AR | Reclassification only; a rejection changes nothing unless it lowers expected recovery (for example timely-filing risk), in which case the estimate is updated |
| Adjudication received | Only the difference between the estimate and the adjudicated evidence | Realize the contractual adjustment against the allowance; true-up any difference through revenue deduction; transfer patient share between control accounts | Allowed amount confirmed; patient share becomes patient AR; no new revenue |
| Remittance posted | None | Dr Cash clearing, Cr Billed AR (payer) | Cash settles the receivable |
| Bank matching | None | Dr Bank, Cr Cash clearing | Reconciliation, not revenue |
| Dispute opened | Only if it lowers the constrained estimate | Hold the disputed amount in the gross-to-net allowance | **Dispute status is an operational attribute; the recognized asset is only the constrained estimate** |
| Dispute resolved | **Yes**, in the period of resolution if the outcome differs from the held estimate | Release or increase the allowance through revenue deduction | Late recovery or loss is a change in transaction price |
| Later patient or payer non-payment of an amount expected to be collected | **No** (expense instead) | Dr Credit-loss expense, Cr Credit-loss allowance | Credit loss, a separate P&L line |
| Overpayment identified or recoupment | If a change in price: reduces revenue; the amount to return is a liability | Dr Revenue deduction (if applicable), Cr Refund liability; on repayment Dr Refund liability, Cr Cash | Liability from the moment it is identified |
| Approved reversal | Opposite of the original | New reversing batch referencing the original | Never edit the original |

#### 5A.2.5 The estimate before any claim exists

Evidence hierarchy, strongest first:

1. **Fee schedule or contract terms** for the payer and service category, effective-dated.
2. **Adjudication history** for comparable cohorts (same payer class, service category, claim type), including denial, short-pay and recoupment experience.
3. **Portfolio collection rate** by payer class, by cash collected against billed over a stable window. Aging is used to **test** the estimate, not to set it.
4. **Approved judgmental overlay** (for example a new payer, a system outage, a policy change), documented with evidence and an approver.

Method: expected value over a portfolio of similar services (606-10-32-8), with one method applied consistently (32-9). The constraint reduces the amount for denial/dispute risk when experience is limited or outcomes have a wide range. **No precision is invented**: when no reliable evidence supports recovery of a disputed amount, the recognized amount excludes it.

Governance of the estimator:

- Each estimate record carries estimator version, input data window, cohort ID, evidence references and approver; stored in openPractice.
- Estimator changes (new version) need approval and take effect prospectively from a stated effective date; the effect on the open period is shown.
- Back-testing: compare estimate to final entitlement by cohort every close; unexplained variance above a threshold the accountant sets raises a work item.
- Automation may calculate and propose; a named human approves versions, overlays and period-end estimate summaries.

#### 5A.2.6 Classification, allowances and what is not an asset

| Question | Proposal |
|---|---|
| Unbilled vs billed | Separate "unbilled AR" (service done, claim not yet accepted) from "billed AR"; the accountant decides whether unbilled is presented as an unconditional receivable or as a contract asset. Recommended default: **unbilled receivable presented separately until claim submission**, subject to accountant confirmation |
| Transaction-price allowance | Holds contractual adjustments not yet realized, implicit price concessions and the constrained part for denials/disputes. Movements go through **revenue deductions** |
| Credit-loss allowance | Holds only expected credit losses on amounts the practice expected to receive. Movements go through a **credit-loss expense** (separate line). Zero in the main example |
| Dispute status | Operational flag in openPractice (opened, appealed, resolved, amount contested). **It is not an asset.** The recognized asset is only the constrained estimate; contested amounts above it sit inside the transaction-price allowance |
| Self-pay and charity care | Charity care is not revenue; an implicit price concession reduces revenue; a later deterioration of a patient's ability to pay is a credit loss (PwC 5.2). The classification reason is recorded at the time of the decision |

#### 5A.2.7 Special balances

- **Prepaid patient cash:** held as a contract liability until service is performed; at service, applied to the patient receivable. No revenue on receipt.
- **Patient share transfer:** between receivable control accounts; creates no revenue.
- **Cash clearing:** receipts post to cash clearing; the bank match moves them to Bank; a difference opens a reconciliation item, never an automatic correction.
- **Overpayments, recoupments and refunds:** when identified (knowingly received or retained), recorded as a refund liability; repayment follows named-approver release. Medicare overpayment rules (42 CFR 401.305) are a reason to expect a 60-day report-and-return clock; the compliance team owns that clock, not accounting.

#### 5A.2.8 Closing the books

**Four dates on every revenue record.**

| Field | Meaning |
|---|---|
| `recorded_at` | When openPractice stored the record |
| `service_date` | Date the service was performed |
| `effective_date` | Economic date of the new information (for example adjudication date) |
| `accounting_date` | The **open** period the posting belongs to |

**Close procedure (proposed).**

1. **Daily/event-driven batches** post accumulated events with idempotency keys; nothing waits for month end.
2. **Cutoff:** services performed on or before the period end that have no claim yet are included as unbilled AR at the constrained estimate; services after period end are excluded.
3. **Period-end estimate run:** re-run the estimator for open balances; compare to prior estimate; post the difference as a revenue adjustment.
4. **Reconciliation:** (a) gross AR in openPractice equals ERP gross AR control; (b) gross-to-net allowance equals the sum of openPractice estimates; (c) net AR equals gross AR minus allowances; (d) net AR equals cumulative net revenue minus cash and clearing (this is proved for the example); (e) cash clearing equals unmatched deposits.
5. **Close snapshot:** openPractice stores a locked snapshot (balances, estimates, versions, evidence hashes) so any closed period can be reproduced exactly.
6. **Approval:** a named person approves the snapshot before the ERP period is closed.

**Late information after close.**

| Situation | Treatment |
|---|---|
| New fact arriving after close about the **future** (for example appeal result on day 90) | A change in estimate: recognized in the open period (606-10-32-14, 32-42/43; ASC 250-10-45-17). Closed periods are not changed |
| Information arriving **before the statements are issued** that confirms a condition at the balance-sheet date (ASC 855-10-25-1) | A recognized subsequent event. Handle as an adjustment to the closing period **only if it is still open or being reopened under approval**; otherwise assess materiality and record in the next period |
| Fact that was **already available and overlooked or misapplied** | An error (ASC 250-10-45-23), not an estimate change; the accountant assesses materiality under the practice's approach; material errors are corrected by reopening or restating, immaterial ones by out-of-period or next-period adjustment, with disclosure per policy |
| Reopening a closed period | Neither blanket-forbidden nor casual: needs the accountant's approval, a materiality note and a new snapshot version; the previous snapshot remains reproducible |
| Backdating | Never silently. A posting uses `accounting_date` of the open period; the economic date is kept in `effective_date` |

#### 5A.2.9 What the ERP receives (benign-category contract)

- **Lines are categories only**, for example `SVC-OFFICE-VISIT`, `SVC-LAB-WORK`, `SVC-LAB-TEST`, plus control accounts. Mapping from openPractice service codes to categories is **versioned and effective-dated**; changes need approval and apply prospectively, with remapping handled as a posted reclassification.
- **Never sent to the ERP:** patient identifiers, diagnosis or procedure codes, payer correspondence, claim content or remittance detail. The ERP receives a batch ID, amounts, category, `accounting_date`, `effective_date`, estimator version and a detail-manifest hash.
- **Drill-through is restricted to openPractice;** ERP users see a batch ID, not clinical or claim content.
- **Payer class** (for example Medicare, commercial, self-pay) is a candidate control-account dimension. It is non-clinical, but it is a policy question for Brett whether even this is too revealing.

#### 5A.2.10 Controls and authority

By default, **no autonomous financial or patient-impacting action.** Automation may calculate estimates, prepare batches, flag variances and propose reversals; a named human approves estimator versions, period-end snapshots, write-offs, refunds, reversals, reopenings and any reclassification. Every action records actor, time, before and after reference and source message (Section 7.3).

### 5A.3 Worked example (synthetic)

**Service:** office visit, category `SVC-OFFICE-VISIT`. Currency USD, shown in dollars and cents. All numbers below were produced and checked by a calculation, and the table values below are copied from that output.

**Assumptions.**

- Day 1: service performed. List (gross) amount 200.00. Fee schedule allows 120.00: payer 96.00, patient 24.00.
- Estimator (portfolio, expected value, constrained) sets the net expected amount at **114.00**: the 120.00 allowed less a 6.00 constraint for expected short-pay/denial on payer share. Contractual adjustment 80.00 + constraint 6.00 = gross-to-net 86.00. Patient share assumed fully collectible, so credit-loss allowance is 0.
- Day 10: claim billed at 200.00.
- Day 60: payer allows 120.00 as expected, pays 84.00, denies 12.00 (disputed); patient responsibility 24.00. The 12.00 is excluded from the recognized asset because there is no basis to say a significant reversal is not probable.
- Day 61: payer deposit matched at bank. Day 75: patient pays 24.00, deposit matched day 76.
- Day 90: appeal overturns 8.00, upholds 4.00; payer pays 8.00 (deposit day 91).
- Illustrative period cutoffs only (relative, not computed dates): **P1** closes after day 10 and before day 60; **P2** closes after day 76 and before day 90; **P3** closes after day 91. P1 and P2 close before final settlement.
- Presentation: gross line with a deduction, so gross AR and the allowance are visible. A net-posting presentation gives the same net revenue and net AR.

**Journals (all balanced; each entry's debits equal credits).**

| Journal | Day | Period | Entry | Account | Dr | Cr |
|---|---:|---|---|---|---:|---:|
| J1 | 1 | P1 | Service performed: gross charge | Unbilled AR (gross) | 200.00 |  |
|  |  |  |  | Service revenue - gross charges |  | 200.00 |
| J2 | 1 | P1 | Gross-to-net estimate (contractual 80.00 + constraint 6.00) | Revenue deductions | 86.00 |  |
|  |  |  |  | Gross-to-net allowance |  | 86.00 |
| J3 | 10 | P1 | Claim submitted: unbilled to billed | Billed AR - payer (gross) | 200.00 |  |
|  |  |  |  | Unbilled AR (gross) |  | 200.00 |
| J4 | 60 | P2 | Adjudication: contractual adjustment realized | Gross-to-net allowance | 80.00 |  |
|  |  |  |  | Billed AR - payer (gross) |  | 80.00 |
| J5 | 60 | P2 | Estimate update: denial 12.00 held out of the asset (allowance 6.00 to 12.00) | Revenue deductions | 6.00 |  |
|  |  |  |  | Gross-to-net allowance |  | 6.00 |
| J6 | 60 | P2 | Patient share transferred (no revenue) | Patient AR (gross) | 24.00 |  |
|  |  |  |  | Billed AR - payer (gross) |  | 24.00 |
| J7 | 60 | P2 | Payer remittance 84.00 | Cash clearing | 84.00 |  |
|  |  |  |  | Billed AR - payer (gross) |  | 84.00 |
| J8 | 61 | P2 | Bank receipt of payer deposit | Bank | 84.00 |  |
|  |  |  |  | Cash clearing |  | 84.00 |
| J9 | 75 | P2 | Patient payment 24.00 | Cash clearing | 24.00 |  |
|  |  |  |  | Patient AR (gross) |  | 24.00 |
| J10 | 76 | P2 | Bank receipt of patient deposit | Bank | 24.00 |  |
|  |  |  |  | Cash clearing |  | 24.00 |
| J11 | 90 | P3 | Appeal result: 8.00 overturned, allowance released | Gross-to-net allowance | 8.00 |  |
|  |  |  |  | Revenue deductions |  | 8.00 |
| J12 | 90 | P3 | Payer remittance 8.00 on appeal | Cash clearing | 8.00 |  |
|  |  |  |  | Billed AR - payer (gross) |  | 8.00 |
| J13 | 90 | P3 | Upheld denial 4.00 written off against allowance (no P&L) | Gross-to-net allowance | 4.00 |  |
|  |  |  |  | Billed AR - payer (gross) |  | 4.00 |
| J14 | 91 | P3 | Bank receipt of appeal deposit | Bank | 8.00 |  |
|  |  |  |  | Cash clearing |  | 8.00 |

**Rolling balances at each close.**

| Measure | Close P1 | Close P2 | Close P3 |
|---|---:|---:|---:|
| Unbilled AR (gross) | 0.00 | 0.00 | 0.00 |
| Billed AR - payer (gross) | 200.00 | 12.00 | 0.00 |
| Patient AR (gross) | 0.00 | 0.00 | 0.00 |
| Total gross AR | 200.00 | 12.00 | 0.00 |
| Less: gross-to-net allowance | 86.00 | 12.00 | 0.00 |
| **Net AR** | 114.00 | 0.00 | 0.00 |
| Cash clearing (undeposited) | 0.00 | 0.00 | 0.00 |
| Bank (cumulative cash received) | 0.00 | 108.00 | 116.00 |
| Cumulative net revenue recognized | 114.00 | 108.00 | 116.00 |
| Net revenue recognized in the period (adjustment) | 114.00 | -6.00 | 8.00 |
| Operational dispute status (not an asset) | 0.00 | 12.00 | 0.00 |
| Credit-loss allowance | 0.00 | 0.00 | 0.00 |


Reading the table: at the P1 close, gross AR 200.00 less allowance 86.00 gives net AR 114.00, equal to cumulative net revenue 114.00 and zero cash. At the P2 close the practice has received 108.00 (84.00 + 24.00), gross AR is 12.00 and is fully covered by the 12.00 allowance, so net AR is zero; cumulative net revenue is 108.00 and the contested 12.00 is **a dispute status, not an asset**. At the P3 close the appeal produces 8.00 more cash and 4.00 is written off against the allowance, leaving no balances; cumulative net revenue is 116.00.

**Checks (proved by calculation).**

- Net AR = cumulative net revenue minus cash collected and clearing: 114.00 = 114.00 − 0; 0.00 = 108.00 − 108.00; 0.00 = 116.00 − 116.00.
- Revenue by period: P1 +114.00, P2 −6.00, P3 +8.00; sum 116.00 = final entitlement (allowed 120.00 minus upheld denial 4.00) = total cash received 116.00 (84.00 + 24.00 + 8.00).
- Gross revenue line 200.00 less deductions 84.00 (86.00 + 6.00 − 8.00) = 116.00.

**Why billing and cash do not double count revenue.** The 200.00 billed amount moved between asset accounts (J3) and had a revenue effect of zero. Cash receipts (J7, J9, J12) only reduced receivables and had a revenue effect of zero. Revenue appeared exactly three times: at service (114.00), when the day 60 evidence lowered the estimate by 6.00, and when the appeal succeeded in part (+8.00). The total never exceeds the final entitlement.

**Transaction-price allowance versus credit-loss allowance.** In this example the gross-to-net allowance moved from 86.00 to 12.00 to zero and every movement affected revenue deductions or was a use of the allowance with no P&L. The credit-loss allowance stayed at 0.00 because every amount the practice expected to collect was collected.

**Brief second scenario (only to show the credit-loss distinction).** Same service, but the patient's 24.00 share, expected to be collectible at day 1, is not paid and the patient later becomes unable to pay (day 120, a later period). The 24.00 was part of the expected entitlement, so revenue stays at its recognized level and the loss is **Dr Credit-loss expense 24.00, Cr Credit-loss allowance (or AR) 24.00**, shown below revenue. If instead the practice had expected on day 1 to collect only 18.00 of that share, the 6.00 difference would have been an implicit price concession and would have reduced revenue at service. Same cash result; different line.

### 5A.4 Accountant approval items (no decision is implied)

1. Reporting basis (accrual US GAAP or IFRS vs cash/tax basis) and how book-to-tax reconciliation is done in the ERP.
2. Estimator method, cohort definitions, constraint thresholds, back-testing variance limits.
3. Gross-line-with-deduction presentation vs net-only posting.
4. Unbilled amounts: unconditional receivable vs contract asset, and presentation.
5. Treatment of the credit-loss standard and wording (Topic 326 vs the Topic 310 text still referenced in 606-10-45).
6. Materiality approach for errors; reopening policy; approval authority.
7. Whether payer class may be a control-account dimension in the ERP.

### 5A.5 Sources (accessed 2026-11-14)

Verified means the page or PDF text was fetched and read in this research. Secondary means only a summary of the standard was read.

| Source | Used for | URL | Status |
|---|---|---|---|
| FASB, ASU 2014-09, Section A (ASC 606): 606-10-25-1(e), 25-7, 32-5 to 32-14, 32-42/43, 45-1 to 45-4 | Contract existence, variable consideration, constraint, receivable vs contract asset | https://storage.fasb.org/ASU%202014-09_Section%20A.pdf | Verified |
| AASB 15 (text of IFRS 15), paras 47, 50 to 56 | IFRS parallels; "highly probable"; refund liability | https://standards.aasb.gov.au/node/3024 | Verified |
| IFRS 15 paras 9(e), 105 to 108 | Collectibility; contract asset vs receivable | IFRS Foundation standard text | Cited from the standard; not re-verified in this research |
| eCFR, 42 CFR 401.305(a)(2), (b)(1), (f) | Overpayment liability and 60-day report-and-return | https://www.ecfr.gov/current/title-42/chapter-IV/subchapter-B/part-401/subpart-D/section-401.305 | Verified |
| PwC Viewpoint, US Health Care Guide 3.3 (variable consideration, 3.3.1 to 3.3.2) | Implicit price concessions, portfolio approach, third-party settlements | https://viewpoint.pwc.com/dt/us/en/pwc/accounting_guides/health-care/health_care_guide/chapter_3_revenue/3_3_variable.html | Verified |
| PwC Viewpoint, US Health Care Guide 5.2 (uncompensated care) | Implicit price concession vs credit loss vs charity care | https://viewpoint.pwc.com/dt/us/en/pwc/accounting_guides/health-care/health_care_guide/chapter5_othertopics-/52_uncompensatedservices.html | Verified |
| HFMA, Issue Analysis: Revenue Recognition | Portfolio approach; aging as a test; bad debt vs price concession presentation | https://www.hfma.org/wp-content/uploads/2023/08/Issue-Analysis-Revenue-Recognition-FINAL.pdf | Verified |
| EY, Technical Line TL03540-171US (2023-12-18) | Collectibility and implicit price concession vs credit risk | https://www.ey.com/content/dam/ey-unified-site/ey-com/en-us/technical/accountinglink/documents/ey-tl03540-171us-12-18-2023.pdf | Verified |
| ASC 855-10-25-1, 25-3 (subsequent events) | Recognized vs nonrecognized | https://viewpoint.pwc.com/dt/us/en/fasb/GAAP/Codification/Codification/Codification/Broad_Transactions/Subsequent_Events/Overall/855-10-25.html ; https://asc.understandingaccounting.org/asc/855/10/25.md | Secondary |
| ASC 250-10-45-17, 45-23 (estimates vs errors) | Change in estimate vs error correction | https://asc.understandingaccounting.org/asc/250/10/ ; https://viewpoint.pwc.com/dt/us/en/pwc/accounting_guides/financial_statement_/financial_statement___18_US/chapter_30_accountin_US/307_correction_of_an_US.html | Secondary |
| AICPA Audit and Accounting Guide, Health Care Entities, ch. 10 | Additional healthcare reference | https://viewpoint.pwc.com/dt/us/en/aicpav2/aag-hco/aag-hco/aag-hco10.html | Pointer only; not read |

This section is a design proposal, not accounting or legal advice. The final policy needs the practice's accountant.

---

## 6. End-to-end synthetic flow (single patient)

Synthetic patient "Pat Example" (not a real person). Steps marked **[conditional]** depend on payer rules, the service, or jurisdiction and are not universal requirements.

| # | Step | Context | What happens | Evidence/ID produced |
|---|---|---|---|---|
| 1 | Registration | C2, C3 | Create Party; roles: patient; link to EHR patient ID; capture administrative data and financial consents (e.g. assignment of benefits) | `Party P-001`, EHR xref, consent record |
| 2 | Coverage capture | C5 | Record Medicare coverage; subscriber is self | `Coverage C-001` |
| 3 | Coverage verification **[conditional]** | C5 | Run eligibility check where the pathway supports it; store response as evidence | `EligibilityCheck E-001` |
| 4 | Authorization **[conditional]** | C5 | Only if the service/payer requires it; record or flag a work item | `Authorization A-001` or none |
| 5 | Appointment | C3 | Optional in MVP if EHR provides the visit reference | `Appointment AP-001` |
| 6 | Clinical care and signing | C1 (openChart) | Encounter documented and signed | Encounter ref `ENC-9`, version 3 |
| 7 | Billable evidence delivered | C1→C6 | EHR sends evidence event: encounter ref, version, provider, date of service, service/diagnosis references as documented, signed-at, provenance. Medical necessity/clinical eligibility and coding are checked **[conditional]** according to the pathway; coding supplied by EHR or by a billing coder (a human) as the practice decides | `Evidence EV-1` |
| 8 | Charge capture | C6, C4 | Price from fee schedule; create Charge referencing EV-1; duplicate guard on (encounter ref, service, date). List price is an operational figure; revenue is the constrained expected amount per Section 5A, posted by category when the service is performed | `Charge CH-1`, list amount 150.00 USD (synthetic) |
| 9 | Claim build and review | C6 + Adapter | Build claim through the Medicare pathway adapter; scrub; human review before submission | `Claim CL-1` v1 |
| 10 | Submission | Adapter | Send; receive acceptance or **rejection** (rejection: fix and resubmit as v2, no financial effect) | `ClaimStatusEvent S-1` |
| 11 | Adjudication / remittance | C7 | Remittance: allowed 100.00, paid 80.00, patient responsibility 20.00, contractual adjustment 50.00 (synthetic numbers; the estimate is trued up only by any difference from what was expected) | `Remittance R-1` |
| 12 | Posting of adjudication | C7→C8 | Payer payment, realization of the contractual adjustment against the allowance, any estimate true-up and transfer to patient responsibility posted per 5.4; the fully worked monetary example with period closes is in Section 5A.3 | `PostingBatch PB-1` → ERP doc ID |
| 13 | Patient responsibility | C6 | Patient balance 20.00; statement issued | `Statement ST-1` |
| 14 | Patient payment | C7 | Receipt 20.00; allocated to the balance | `Payment PM-1`, allocation |
| 15 | Posting of receipt | C7→C8 | Cash clearing vs patient receivable | `PostingBatch PB-2` |
| 16 | Reconciliation | C7, C8 | Subledger = ERP control accounts; deposits matched to cash clearing | `Reconciliation RC-1` closed |

### 6.1 Correction and reversal path

| Situation | Handling |
|---|---|
| EHR amends or corrects a signed note after charge capture | EHR emits a **correction event** referencing the earlier version; openPractice flags the charge/claim, **does not silently change it**; a human decides replacement claim vs void |
| Claim already paid but must be corrected | Replacement or void claim (per payer pathway) as new claim version; original retained |
| Payer recoups after payment | Negative remittance line; reversal posting referencing original PB; balance recalculated |
| Patient payment misapplied | Reverse allocation (new record), reallocate; if posted, post reversing batch and a new one |
| Refund | Refund record with named approver; reversing posting |
| Posting error | Reversing batch referencing original; never edit or delete in the ERP |

---

## 7. Minimal integration contract examples

### 7.1 Common envelope (all messages)

| Field | Rule |
|---|---|
| `message_id` | Unique per message |
| `idempotency_key` | Same key and same payload ⇒ same effect; same key with different payload ⇒ rejected |
| `schema_version` | Explicit; consumers reject unknown major versions |
| `source_system`, `source_record_id`, `source_record_version` | Provenance of the originating fact |
| `occurred_at`, `recorded_at` | Both, with time zone |
| `correlation_id` | Links the chain (encounter → charge → claim → posting) |
| `supersedes` | For corrections: ID of the message or version replaced |
| `actor` / `authority` | Who or what acted and under which grant |

### 7.2 Example contracts

| Contract | Direction | Content (summary) | Must not contain |
|---|---|---|---|
| **Billable evidence event** | EHR → openPractice | Encounter ref and version, patient ref (EHR ID), provider ref, date of service, documented service/diagnosis references, signed-at, provenance, correction link | Full note or chart, results, medication lists |
| **Correction event** | EHR → openPractice | Same envelope, `supersedes` = prior version, reason category | Free-text clinical narrative |
| **Appointment/encounter reference** | Either way | IDs, times, status | Clinical content |
| **Claim submission / response** | openPractice ↔ Adapter ↔ payer | Claim version; statuses; rejection vs denial codes carried raw plus normalized | Anything beyond the pathway's minimum |
| **Remittance** | Adapter → openPractice | Remittance ID, claim refs, amounts by line, reason codes (raw + normalized), payer reference | — |
| **Accounting posting batch** | openPractice → ERP | Batch ID, version, idempotency key, period, currency, lines (account, amount, direction, benign service category, optional payer class), `accounting_date`, `effective_date`, estimator version, detail-manifest hash | Patient names or identifiers, diagnosis or procedure codes, notes, payer correspondence, claim content |
| **Posting acknowledgement** | ERP → openPractice | Batch ID, ERP document ID, status, rejection reason | — |
| **Automation request (MedxFactory)** | Agent → openPractice | Capability name, scope, proposed action, requested grant | Authority to act without a grant |

### 7.3 Value and safety rules

- **Money:** integer minor units plus ISO 4217 currency code; never floating point; rounding rule stated once per contract; one currency per batch.
- **Least privilege:** each integration identity holds only the grants it needs (the EHR can write evidence, not read balances; the ERP adapter can write postings, not read coverage; a scrubber cannot submit).
- **No chart copying:** references plus the minimum structured fields needed; the EHR remains the place to read the chart on demand.
- **Corrections** are new events with `supersedes`; originals stay.
- **Capability declaration:** each adapter and EHR connection advertises what it supports (e.g. eligibility, authorization, attachments); openPractice offers only what is declared.
- **Human authority:** by default no autonomous patient-impacting or financial action. Claim submission, write-off, refund, adjustment approvals and patient statements require a named human approval or an explicit, revocable, narrowly scoped grant. Automation may *prepare*, *propose* and *flag*.
- **Audit:** every state change records actor, time, before/after reference, and the source message.

---

## 8. Jurisdiction isolation (Medicare)

Medicare jurisdiction and claim pathway are unspecified. They do not block the framework because the following are placed behind a **Claim Pathway Adapter**:

| Behind the adapter (jurisdiction/pathway-specific) | In the core (stable) |
|---|---|
| Claim format and submission channel | Charge, Claim, ClaimLine, ClaimStatusEvent model |
| Eligibility check channel and response parsing | EligibilityCheck evidence record |
| Code sets, edits, required fields, attachments | A claim "readiness" checklist that the adapter populates |
| Reason-code normalization tables | Raw + normalized denial/rejection fields |
| Timely-filing and similar rules | Work-item and deadline mechanism |

The core needs only: a claim version, line amounts, and a status event stream. Questions to settle later appear in D6 and Section 10; no jurisdiction question is needed now.

---

## 9. MVP include/defer matrix and build sequence

### 9.1 Matrix

| Capability | MVP | Defer | Notes |
|---|---|---|---|
| Party/roles (patient, guarantor, subscriber, provider) | ✔ | | Provider/staff minimal |
| Administrative registration and financial consent | ✔ | | |
| Medicare coverage record | ✔ | | |
| Other payers/secondary payer | | ✔ | Model must allow later |
| Eligibility verification | Conditional | | If pathway supports |
| Authorization tracking | Conditional | | Record only; no workflow engine |
| Appointment scheduling | Minimal/reference | ✔ full calendar | Only if EHR cannot provide visit reference |
| Billable evidence intake, charge capture | ✔ | | |
| Fee schedule (single) | ✔ | Multiple, packages, retail | |
| Claim build, review, submit via adapter | ✔ | | |
| Rejection/denial work queue | ✔ basic | Appeals automation | |
| Remittance posting, allocation | ✔ | | |
| Patient balance, statement, payment recording | ✔ | Payment plans, online portal | |
| Accounting posting + reconciliation (one ERP) | ✔ | Second ERP adapter | |
| Corrections/reversals | ✔ | | |
| Audit and roles | ✔ | | |
| Staff management, payroll, HR | | ✔ | Out of MVP |
| Inventory, retail, memberships | | ✔ | |
| Reporting | Basic (aging, claim status) | Analytics | |
| MedxFactory automation | | ✔ | After contracts stable; propose-only first |

### 9.2 Build sequence (tied to the flow in Section 6)

| Stage | Delivers | Flow steps proven |
|---|---|---|
| 1 | Cross-cutting: roles, audit, envelope, idempotency | all |
| 2 | Party, roles, registration, financial consent, coverage record | 1–2 |
| 3 | Catalog + fee schedule; evidence intake; charge capture; correction handling | 7–8, 6.1 |
| 4 | Claim model, review gate, adapter stub (fake payer) | 9–10 |
| 5 | Remittance, adjustments, allocation, patient balance and statement, payments | 11, 13–14 |
| 6 | Accounting posting contract with one ERP adapter, reconciliation | 12, 15–16 |
| 7 | Real Medicare adapter once pathway known; conditional eligibility/authorization | 3–4, real 10–11 |
| 8 | Deferred scope as needed | |

Rationale: stages 1–6 can be built and tested end-to-end with synthetic data and a simulated payer before the Medicare pathway is selected.

---

## 10. Decisions for Brett (recommended defaults)

| ID | Decision | Recommended default | Why |
|---|---|---|---|
| D1 | One `Party` identity with roles; "customer" is not a patient role | **Yes** | Prevents competing identities |
| D2 | Receivable subledger owner | **openPractice** (Option A); ERP gets summarized control postings | Avoids double AR; ERP-neutral |
| D3 | Which ERP first | **ERPNext first as the reference adapter**, MSBC adapter second, one active per deployment | Same Frappe stack eases early testing; the contract stays neutral. Override if the practice already runs MSBC |
| D4 | Deployment: one Frappe app `open_practice` with internal modules, no dependence on ERPNext doctypes | **Yes** | Keeps MSBC option open; domain lines enforced in code review, not by service splits |
| D5 | Revenue recognition basis in postings | **Corrected:** net revenue = constrained expected entitlement at service, updated each close; gross charge is memo or gross line with its deduction; billing, adjudication, cash and disputes are evidence events (Section 5A). The first version's "gross at charge plus adjustment on adjudication" is withdrawn | Avoids overstating revenue and AR at each close; allows timely close; final policy to be approved by the practice's accountant |
| D6 | Who supplies coding and medical-necessity evidence | **EHR supplies documented references; a human biller confirms**; openPractice does not infer codes | Preserves clinical authority; conditional on pathway |
| D7 | Automation default | **Propose-only; named human approval for submit, write-off, refund, statement** | Preserves financial authority and reversibility |
| D8 | Revenue-recognition policy approval and close governance (Section 5A.4) | **Accountant approves reporting basis, estimator, classification, materiality and reopening policy before go-live** | Policy choices are accounting judgments, not software defaults |

Nothing above is ratified until Brett records a decision.

## 11. Future questions (only where genuinely needed, not blocking)

- Medicare jurisdiction and claim pathway (needed at stage 7).
- Reporting basis and the first estimator parameters (D5, D8), to be set with the accountant.
- Whether secondary payer or patient online payment is needed soon after MVP.
- Whether any EHR other than openChart must be supported early (affects the evidence contract, not the domains).
