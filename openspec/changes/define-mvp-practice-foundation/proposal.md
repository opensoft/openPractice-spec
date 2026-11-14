Status: draft

## Why

openPractice has no ratified statement of what its minimum viable product must contain at the seams it shares with other systems. The ideation packet sequences a small-practice beta in six slices, but it leaves four boundaries implicit: what an administrative patient record is and how it is maintained, where the line falls between openPractice and a connected electronic health record (EHR), where billing ends and accounting begins, and how money reaches the practice's books. Two of those are tracked as open items in the packet. Risk R-7 says the EHR boundary is specified in one direction only. The scope document also leaves the accounting destination open.

The product owner, Brett Heap, settled these boundaries on 2026-11-14. This change records his decisions as governed requirements before any runtime exists, so the first Speckit features build against a stated boundary instead of discovering one.

All ideation paths below are relative to the root of `opensoft/openPractice-spec`. Every ideation document cited is `Status: brainstorm` except the framing decisions FD-1 to FD-11 in [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md), which were ratified on 2026-08-27. This change does not contradict any of them.

## The refined MVP statement

The openPractice MVP SHALL include, in addition to whatever beta scope is ratified:

1. **simple administrative patient CRUD**: create, read, update and deactivate an administrative patient record holding name, date of birth, administrative sex, gender identity and pronouns, contacts and address, a guarantor, and one or more prioritized insurance coverages, with audit and provenance on every change;
2. **a defined openChart/EHR boundary**: each system owns its own patient record, the two records are linked by cross-references that each side stores, openPractice publishes administrative demographic changes through a versioned contract, and the clinical facts openPractice needs for charge capture come from the EHR through versioned FHIR R4-compatible contracts;
3. **a defined billing-versus-accounting boundary**: openPractice owns billing and the receivables; the accounting system is the books of record; openPractice never becomes a general ledger or ERP;
4. **an accounting integration contract**: one versioned contract covering ERPNext Accounts, QuickBooks Online and Microsoft Dynamics 365 Business Central, of which the MVP builds only the ERPNext adapter.

### How it maps onto the beta slices

The slicing in [`ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md) remains a non-normative hypothesis. This change does not ratify it. It states where the four items fall within it:

| MVP item | Slice | Why there |
|---|---|---|
| (a) patient CRUD | **Slice 0** | The patient, guarantor and subscriber records are part of Slice 0's "party and place register", the substrate every later slice references. |
| (b) EHR boundary: patient linkage and demographic publication | **Slice 0** | Linkage is a property of the patient record and must exist before Slice 1 books against a linked patient. |
| (b) EHR boundary: clinical facts for charge capture | defined here, **consumed in Slice 3** | Slice 3 creates charges "from a completed-event or EHR charge-derivation reference"; R-7 asks for the boundary to be settled before Slice 3. |
| (c) billing-versus-accounting boundary | **Slice 0** | Slice 0's "Immutable Money Ledger And Reason Taxonomy" is defined before anything posts because the taxonomy is "the join key to the general ledger". |
| (d) accounting integration contract and ERPNext adapter | **Slice 4** | Slice 4 carries "Accounting Export, QuickBooks-Shaped" with "Export-Batch Freeze". This change replaces that capability with the contract and the ERPNext adapter. |

## What Changes

- Add a `patient-registry` capability: the administrative patient record and its fields, defined carefully for administrative sex, gender identity and pronouns; contacts and addresses with history; exactly one current guarantor (self or another person); zero or more prioritized coverages with versioning; exact-match search before create with no scoring; delete as deactivation; hard delete only for a record created in error with no linked activity, reconciled with `add-reimbursement-retention-control`; audit of every change and every PHI read; synthetic-only fixtures.
- Add an `ehr-boundary` capability: separate, linked patient records; explicit and human-confirmed link creation; an identity-review state for linkage conflicts; demographic publication under the contract `openpractice-ehr-patient-link/1.0`; clinical facts for charge capture under `openpractice-ehr-clinical-facts/1.0` (FHIR R4-compatible); forward-only clinical corrections; no direct database access in either direction; operation with no EHR; appointment authority explicitly left undecided.
- Add a `billing-accounting-boundary` capability: openPractice owns claims, patient statements, payer and patient receivables, payment allocation, refunds, collections and financial audit lineage, and is the receivable source of truth. The accounting system owns the general ledger, bank accounts, taxes, payroll, payables and the final financial statements, and mirrors openPractice. The change also covers immutability at post time, dated reversals, versioned immutable export batches (FD-3, generalized to any destination), the reason taxonomy as the general-ledger join key, and no money movement originated through the integration.
- Add an `accounting-integration` capability: one contract, `openpractice-accounting-integration/1.0`, with these operations: customer upsert, invoice, payment, credit note, reversal, refund, deposit, summary journal, batch export and reconciliation report. It also defines deterministic idempotency keys, versioned immutable batches, explicit human release, error and retry semantics, posting mode as a per-destination setting, a BAA gate before patient-level mode, PHI minimization, isolated adapters with no import-time ERPNext dependency, and contract tests.
- Record the companion change in openChart's spec leg: `opensoft/openChart-spec`, change `define-openpractice-boundary`. It specifies openChart's side of the same two EHR contracts, using the same contract names, versions and identifier namespaces.
- Require an unconditional owner ratification (`owner-ratification.md`) after strict validation and before any Speckit feature is created.

## What this supersedes or resolves

- **Supersedes the deposit-level summary export as the default.** [`ideation/brainstorm/openpractice-small-practice-beta-money-lines-and-bank-inbox.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-money-lines-and-bank-inbox.md) §8 "The export, and the only lock in the product" (around line 577) and the "Accounting Export, QuickBooks-Shaped" row of the feature-normalization register make one journal entry per bank deposit, cash basis, the default. Brett Heap chose **patient-level invoices** as the default posting granularity on 2026-11-14, knowing this puts PHI in the accounting system. A deposit-level, no-PHI **summary mode** survives only as the per-destination fallback for a destination without a verified Business Associate Agreement (BAA). The "cash basis by default" sentence is affected too. Posting invoices puts receivables in the books, so whether the practice can still *report* on a cash basis becomes a per-destination question (see design, Open Questions).
- **Resolves the open question on the accounting destination for the MVP.** The scope-and-sequencing document asks which "accounting destination define[s] the first contract-test suite", and [`ideation/brainstorm/openpractice-small-practice-beta-financial-control-and-accounting-bridge.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-financial-control-and-accounting-bridge.md) asks which accounting system "should receive the first beta export". The answer is **ERPNext Accounts first**. QuickBooks Online and Business Central follow after the MVP against the same contract.
- **Generalizes FD-3's export-batch freeze** from "exported to QuickBooks" to any accounting destination. Every other part of FD-3 is kept unchanged: immutability at post time, dated reversals, as-of queries, the daily balanced day, and corrections landing in the next batch with back-references.
- **Settles the patient-identity half of R-7**: each system owns its own record, and the two are linked. **The appointment-authority half of R-7 stays open** and is recorded as an open question; this change does not decide it.
- **Partly supersedes the direction of flow** in [`ideation/brainstorm/openpractice-feature-list-interoperability-and-exchange.md`](../../../ideation/brainstorm/openpractice-feature-list-interoperability-and-exchange.md), which has openPractice consuming Patient and Coverage resources from openChart. Under this change openPractice is the source of the administrative patient and its coverage and *publishes* demographics. It consumes only the clinical facts that charge capture needs.
- **Departs from the recommendation for the first staged proposal.** Scope-and-sequencing recommends that the first OpenSpec proposal be one vertical thread through Slices 0 to 4, after the Thread contract. This change stages boundaries first. It does not author the Thread contract (FD-7), and it requires that the Thread contract exist before the accounting adapter feature starts (see `tasks.md`).

## Non-goals

- **Duplicate scoring and the merge queue.** The MVP registry shows exact matches only and never merges. See "Identity-safe record creation" in [`ideation/brainstorm/openpractice-feature-list-registration-and-demographics.md`](../../../ideation/brainstorm/openpractice-feature-list-registration-and-demographics.md) and "Patient Merge Aftermath And Reversal" in the feature-normalization register.
- **Insurance-card OCR.** See "Coverage capture from cards" in the registration feature list.
- **Household modeling.** See "Household And Family Coordination" and "Minor-To-Adult Authority Transition" in [`ideation/brainstorm/openpractice-small-practice-beta-feature-normalization.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-feature-normalization.md).
- **QuickBooks Online and Business Central adapters.** The contract covers them; building them is post-MVP.
- **Deciding which system is authoritative for appointments** (R-7).
- **Authoring the Thread contract** (FD-7), the reason taxonomy's categories, or any posting automation beyond explicit human release.
- **Any general-ledger, payables, payroll, tax or financial-statement function inside openPractice.**
- **Asserting that any accounting vendor will or will not sign a BAA.** That must be verified per vendor.
- **Runtime code, dependencies, or changes to another repository.** openChart's side is governed by its own change.

## Capabilities

### New Capabilities
- `patient-registry`: The administrative patient record owned by openPractice. It covers demographics with distinct administrative-sex, gender-identity and pronoun attributes; contacts and addresses; the guarantor; prioritized and versioned coverages; exact-match search before create; deactivation as delete; tightly bounded hard delete; and audit of every change and read.
- `ehr-boundary`: Separate, linked patient records. It covers human-confirmed link creation, identity review for conflicts, demographic publication under `openpractice-ehr-patient-link/1.0`, clinical facts for charge capture under `openpractice-ehr-clinical-facts/1.0`, forward corrections, no direct database access, operation with no EHR, and appointment authority left undecided.
- `billing-accounting-boundary`: The split between billing ownership and the books of record. It covers openPractice as receivable source of truth, the accounting system as mirror, immutability and dated reversals, versioned immutable batches, the reason taxonomy as join key, the bank-line division of labour, and no money movement through the integration.
- `accounting-integration`: The contract `openpractice-accounting-integration/1.0` and its adapters. It covers operations, idempotency, batches, explicit release, retries, reconciliation, per-destination posting modes, the BAA gate, PHI minimization, adapter isolation, credentials and contract tests.

### Modified Capabilities

None. openPractice has no canonical capability specs yet. This change does not modify `add-reimbursement-retention-control`; it depends on that change for hard-delete gating.

## Impact

- Establishes the first openPractice data contracts for the patient, guarantor, coverage, EHR link, demographic outbox, export batch, posting-mode setting and BAA record. It also fixes three external contract identifiers that openChart and future adapters will implement.
- Puts PHI into a third party's system when a destination runs in patient-level mode. That widens the attack surface and the business-associate footprint. Constitution Principle II's security review and the living risk analysis must cover every destination before patient-level mode is enabled for it.
- Requires contract tests at three new integration boundaries (EHR patient link, EHR clinical facts, accounting destination), all using synthetic fixtures and contract doubles (Principle III).
- Commits openChart to new work, governed in `opensoft/openChart-spec` change `define-openpractice-boundary`: accepting links and demographic updates, holding conflicts in identity review, and publishing clinical facts. openChart cannot publish billable clinical facts until it has a governed clinical-encounter capability; until then, charge capture against openChart waits.
- Adds no runtime code, dependencies, vendor selections beyond the ERPNext-first decision, legal rules, or BAA claims.
