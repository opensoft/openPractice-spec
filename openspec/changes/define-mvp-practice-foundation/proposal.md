Status: draft

## Why

openPractice has no ratified statement of what its minimum viable product must contain at the boundaries it shares with other systems. The ideation packet sequences a small-practice beta into six slices, but it leaves four boundaries implicit:

- what an administrative patient record is and how it is kept;
- where openPractice ends and a connected electronic health record (EHR) begins;
- where billing ends and accounting begins;
- how money reaches the practice's books.

Two of these are tracked as open items in the packet. Risk R-7 says the EHR boundary is specified in one direction only. The packet also leaves the accounting destination open.

Brett Heap, the product owner, settled these boundaries on 2026-11-14 and ruled on 33 follow-up design questions the same day. Ruling 25 supersedes ruling 3, and ruling 30 supersedes ruling 22. The rulings are recorded in `design.md` under "Owner rulings (2026-11-14)". On 2026-11-15 he accepted rulings 87 to 89 and 93, which add a retraction reason code to the clinical-facts contract and align its diagnosis and ended-link wording; `design.md` lists those edits under "Follow-up of 2026-11-15". This change records the decisions and rulings as governed requirements before any runtime exists. **Rulings are not ratification:** the change stays draft until the owner completes `owner-ratification.md`.

All ideation paths below are relative to the root of `opensoft/openPractice-spec`. Every cited ideation document is `Status: brainstorm`, except the framing decisions FD-1 to FD-11 in [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md), which were ratified on 2026-08-27. This change contradicts none of them, with two recorded exceptions:

- It adds tier-4 gates beyond FD-5's list: hard delete and `customer-detail` enablement (ruling 12, tension T-13).
- It keeps explicit sign-offs below tier 4 where FD-5 relies on reversibility and delay. These are `link_existing` confirmation, `create_new` and revocation requests, and identity-review resolution (EB-04, EB-05, ruling 12). They also include accepting or declining a correction request and re-asserting a rejected date of birth (EB-08, EB-09), export-batch release (AI-06, avoiding R-2), and the manual-entry attestation under identity review (EB-17). Together these are tension T-17.

Close re-estimates post automatically, as FD-3 requires. Every hold keeps FD-5's tier-3 deadline escape. Signers follow ruling 33.

## The refined MVP statement

The openPractice MVP SHALL include the following, in addition to whatever beta scope is ratified:

1. **Simple administrative patient CRUD.** Create, read, update and deactivate an administrative patient record. The record holds:
   - name, preferred name and preferred language;
   - date of birth;
   - administrative sex, gender identity and pronouns;
   - contacts and address, with confidential-communication instructions;
   - a guarantor;
   - zero or more prioritized insurance coverages.

   Every change and every read is audited.
2. **A defined openChart/EHR boundary.** Each system owns its own patient record, and the two are linked by issuer-qualified cross-references that each side stores. openPractice publishes administrative demographic changes through a versioned contract with a canonical schema. The clinical facts that charge capture needs come from the EHR through a versioned, FHIR R4-compatible contract.
3. **A defined billing-versus-accounting boundary.** openPractice owns billing, the receivables and the explanation of deposits. The accounting system is the books of record, including bank balances and the full bank reconciliation. openPractice never becomes a general ledger or an ERP.
4. **An accounting integration contract.** One versioned contract with a canonical schema covers ERPNext Accounts, QuickBooks Online and Microsoft Dynamics 365 Business Central. The MVP builds only the ERPNext adapter, and that adapter reaches ERPNext through its REST API only.

### How it maps onto the beta slices

The slicing in [`ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md) is still a non-normative hypothesis, and this change does not ratify it.

| MVP item | Slice | Why there |
|---|---|---|
| (a) patient CRUD | **Slice 0** | Slice 0's "party and place register" is the substrate that every later slice references. |
| (b) EHR boundary: linkage and demographic publication | **Slice 0** | Slice 1 books appointments against linked patients, so linkage must exist first. |
| (b) EHR boundary: clinical facts for charge capture | defined here, **consumed in Slice 3** | Slice 3 creates charges from EHR references, and R-7 asks for the boundary to be settled before Slice 3. |
| (c) billing-versus-accounting boundary, including the bank register and payer deposit links | **Slice 0** (taxonomy, register) and **Slice 4** (deposit reconciliation) | The reason taxonomy is "the join key to the general ledger" and must exist before anything posts. Slice 4 is where the money ties out. |
| (d) accounting contract and ERPNext adapter | **Slice 4** | This replaces Slice 4's "Accounting Export, QuickBooks-Shaped". |

## What Changes

- **`patient-registry`.** Adds the administrative record:
  - separate administrative sex, gender identity and pronouns;
  - preferred name and preferred language, with sex assigned at birth and sexual orientation excluded;
  - typed contacts kept as history;
  - confidential-communication instructions that every contact function must honor;
  - exactly one current guarantor, identified by an opaque guarantor account number;
  - zero or more coverages, prioritized and versioned.

  It also defines exact-match search before create; a tamper-evident audit of every change and read; deactivation that still lets services already given be charged; and a tier-4 hard delete limited to records created in error with no linked activity. Such records sit outside every retention record family, and the removal scope is stated exactly. Fixtures are synthetic only.
- **`ehr-boundary`.** Adds:
  - links as (issuer, namespace, value) triples with a configured issuer on each side;
  - one canonical schema per EHR contract, identified by digest;
  - explicit links, with `link_existing` confirmed by a person at tier 2;
  - identity review with a single resolver, where revocation wins;
  - demographic publication under `openpractice-ehr-patient-link/1.0`, with a link-level suspension while the EHR holds an update;
  - correction requests from the EHR;
  - clinical facts under `openpractice-ehr-clinical-facts/1.0`, including a capability result, confirmed diagnoses only (ruling 87), and retractions that carry a reason code, `entered_in_error` or `withdrawn`, that never reveals a restricted act and are still accepted after a link ends (rulings 88 and 89);
  - manual charge entry, marked manual, while no billable facts are available;
  - the FD-5 deadline escape, where manual entry for a visit under identity review needs a staff attestation that identity was verified and documentation is final and signed;
  - no PHI in diagnostics, and no direct database access;
  - appointment authority settled by ruling 31: openPractice owns the administrative appointment, the EHR owns the encounter, and the 1.0 contracts carry no appointment operations.
- **`billing-accounting-boundary`.** Records that openPractice owns billing and the receivables and is their source of truth, and that the accounting system owns the ledger, bank balances and the full bank reconciliation. It also covers:
  - FD-3 extended to every destination;
  - a reason taxonomy mapped per destination and mode;
  - a practice bank account register, with full account and routing numbers encrypted and role-restricted;
  - payer deposit links per payer and billing entity;
  - an expected-account check and a re-enrollment worklist;
  - deposit-only reconciliation, including refund disbursements, with openPractice as the single poster of each deposit it explains and other-route shares posted as `other_route_share`;
  - an offering-route check before charge finalization, so a membership-included service is not invoiced or claimed as medical, with FD-5's tier-3 release at the filing threshold;
  - sign-offs that follow the FD-5 tiers, where a named AI agent with an openWallet ID may sign only opted-in action types and never at tier 4 (ruling 33, BAB-17);
  - a mapped account for the legacy share of a mixed deposit;
  - closed destination periods never edited, with late charges flagged.
- **`accounting-integration`.** Adds `openpractice-accounting-integration/1.0` and its canonical schema, with operations, posting IDs and keys free of personal data, batches released by a person and frozen, delivery states, and a reconciliation report. It also covers:
  - posting mode fixed before a destination's first released batch: `summary-journal`, the default and recommended setup, or `customer-detail`, a per-destination opt-in (ruling 28);
  - customers created only in `customer-detail` mode behind the BAA gate: guarantor accounts, which may carry name and billing address or contact (ruling 25), and payers, with any further field set by a versioned recipient profile;
  - accounting dates (date of service for charges, each event's own date for payments, refunds and deposits), and net revenue at the constrained expected entitlement, estimated at the date of service and re-estimated at each close (ruling 30);
  - the single recipient-profile definition (AI-23);
  - accounting-side customers derived from the guarantor or payer role, not a separate party role (ruling 32);
  - the full set of money events;
  - a BAA gate, or an operator attestation for practice-operated destinations;
  - neutral labels;
  - revenue bank lines read from the destination;
  - per-destination verification before go-live;
  - contract tests.
- **Ownership split with `define-customer-financial-data-boundaries` (ruling 26).** This change owns the posting mechanics of `openpractice-accounting-integration/1.0`: posting identity and idempotency, batches and their freeze, release, retries, the reconciliation report, the posting modes and the BAA gate. Change `define-customer-financial-data-boundaries` (`openspec/changes/define-customer-financial-data-boundaries/`, with its brainstorm packet `ideation/brainstorm/openpractice-customer-financial-separation-overview.md` and companions) owns customer and party roles, routing by offering (insurance-billed healthcare, direct-pay healthcare, membership, retail), obligation ownership and purpose-bound business views. It references this contract for posting. That change and its packet are merged on `main`.
- **Framework document.** `ideation/brainstorm/openpractice-domain-framework.md`, merged on `main`, is a proposal-status brainstorm. Rulings 28 to 31 adopt its Option A and D5/D8, keep demographics with openPractice, and settle appointments. Its posting contract (§5.4, §7) is governed by this change's accounting contract (ruling 26).
- **Companion change.** `opensoft/openChart-spec` change `define-openpractice-boundary` is openChart's side of both EHR contracts. It references this change's canonical schemas by path and digest.
- **Owner ratification.** An unconditional owner ratification (`owner-ratification.md`) is required after strict validation and before any Speckit feature is created.

## What this supersedes or resolves

- **Supersedes the deposit-level, cash-basis summary export as the default.** The default is described in §8 of [`ideation/brainstorm/openpractice-small-practice-beta-money-lines-and-bank-inbox.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-money-lines-and-bank-inbox.md), around line 577, and in the register row "Accounting Export, QuickBooks-Shaped". The new default is **`summary-journal`** (ruling 28): balanced accrual category totals posted to payer-class control accounts, with patient-level receivable detail kept in openPractice and net revenue recognized at the constrained expected entitlement (ruling 30). `customer-detail` invoices, the owner's earlier "patient-level invoices", remain a per-destination opt-in after the BAA gate, chosen before a destination's first release; a destination stays in the mode its first released batch used (AI-09).
- **Supersedes the money-lines brainstorm's bank sources and labels.** openPractice now reads revenue bank lines from the accounting system through the adapter, with statement upload as the fallback. Lines that are not revenue are labelled "accounting system's", replacing "not ours to explain".
- **Resolves the open question of the accounting destination for the MVP:** ERPNext first.
- **Generalizes FD-3's export-batch freeze** from QuickBooks to any destination. This change does not alter or specify the other parts of FD-3.
- **Settles R-7.** Patient identity is settled here, and appointment authority by ruling 31.
- **Partly supersedes the direction of flow** in [`ideation/brainstorm/openpractice-feature-list-interoperability-and-exchange.md`](../../../ideation/brainstorm/openpractice-feature-list-interoperability-and-exchange.md). openPractice publishes demographics and consumes only clinical facts.
- **Departs from the first-staged-proposal recommendation.** That recommendation (a vertical thread first) is not followed. This change stages the boundaries first, does not author the Thread contract (FD-7), and gates Features 3 and 4 on it.

## Non-goals

- **Duplicate scoring and a merge queue.** See [`ideation/brainstorm/openpractice-feature-list-registration-and-demographics.md`](../../../ideation/brainstorm/openpractice-feature-list-registration-and-demographics.md) and "Patient Merge Aftermath And Reversal" in the normalization register.
- **Insurance-card OCR.**
- **Household modeling and the minor-to-adult transition.** See [`ideation/brainstorm/openpractice-small-practice-beta-feature-normalization.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-feature-normalization.md).
- **QuickBooks Online and Business Central adapters.** These come after the MVP.
- **In-process calls into ERPNext.** These depend on counsel.
- **Appointment operations in the 1.0 contracts.** Ruling 31 makes openPractice the appointment's system of record; appointment events come as a later MINOR version.
- **The Thread contract, the reason taxonomy's categories, and automatic batch release.**
- **Routing and owning membership agreements, subscription renewals, retail sales and other non-medical commerce obligations** (rulings 24 and 26). These obligations are owned by the routes of change `define-customer-financial-data-boundaries`, and this contract never creates a second collectible balance for them. Self-pay and direct-pay healthcare charges (FD-4) are medical billing and stay in scope. The customer-field rules of ruling 25 govern only the customers this contract creates.
- **Customer and party roles, offering routes and purpose-bound business views** (ruling 26). These belong to `define-customer-financial-data-boundaries`.
- **Any general-ledger, payables, payroll, tax, financial-statement or full bank-reconciliation function in openPractice.**
- **Any claim about a vendor's BAA availability.**
- **Runtime code and other repositories.**

## Capabilities

### New Capabilities
- `patient-registry` (PR-01 to PR-15): the administrative patient record, its fields, confidential-communication instructions, guarantor, coverages, search, audit, deactivation and bounded hard delete.
- `ehr-boundary` (EB-01 to EB-20): linked records, issuers, canonical schemas, identity review, demographic publication and suspension, correction requests, clinical facts, manual entry, deadline escape and no-PHI diagnostics.
- `billing-accounting-boundary` (BAB-01 to BAB-18): billing ownership, the books of record, FD-3, the taxonomy, the bank register and deposit links, deposit-only reconciliation with a single deposit poster, legacy and other-route shares, late charges and the offering-route check.
- `accounting-integration` (AI-01 to AI-23): the contract and schema, ownership of posting mechanics, adapters, keys, batches, mode, customers created by this contract and their recipient profile, dates, accrual, money events, BAA gate, PHI minimization, bank lines, verification, tests, no posting to records the contract does not own, and the one recipient-profile definition.

### Modified Capabilities

None. This change depends on, and does not modify, `add-reimbursement-retention-control`. When that change is ratified, it must exclude records created in error with no linked activity from its retention record families (ruling 9).

## Impact

- **New openPractice data contracts:** patient, guarantor account, coverage, confidential-communication instructions, EHR link, demographic outbox, practice bank account register, payer deposit link, export batch, posting mode, accounting recipient profile and the BAA or attestation record.
- **External contracts:** three contracts are fixed, with canonical schemas in `contracts/` (`SHA256SUMS` included).
- **PHI exposure:** the default `summary-journal` destination receives category totals by payer class and no PHI. A `customer-detail` destination, an opt-in, receives PHI, limited by default to guarantor account numbers, guarantor names and billing addresses or contacts, dates of service, amounts and neutral labels. Any further field needs a versioned recipient profile (ruling 25). Each destination needs a security review and a risk-analysis entry under Principle II.
- **Contract tests:** required at three boundaries, using synthetic fixtures only.
- **Commitments for openChart:** governed by `define-openpractice-boundary`. Until openChart has a clinical-encounter capability, it reports no billable-facts support, and charges for linked patients are entered manually.
- **Runtime:** no runtime code, dependencies or BAA claims are added.
