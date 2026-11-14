## Context

openPractice is an assembly root with an empty code leg. The Frappe application `open_practice` does not exist yet. The repository constitution (`.specify/memory/constitution.md` in `opensoft/openPractice`, v1.0.0, ratified 2026-08-24) is in force. Its principles shape this design throughout:
- I: HIPAA by design;
- II: security review for PHI, money, authorization and credentials;
- III: test-first, with contract tests at every integration boundary;
- IV: clinical boundary and original implementation;
- V: versioned integration contracts only;
- VI: deployment portability.

Brett Heap, the product owner, made six decisions on 2026-11-14. This change encodes them:
1. the MVP must include patient CRUD, a defined EHR boundary, a defined billing-versus-accounting boundary, and an accounting integration contract;
2. openPractice owns billing, and the accounting system is the books of record;
3. one contract covers ERPNext Accounts, QuickBooks Online and Business Central, and only the ERPNext adapter ships in the MVP;
4. patient-level invoices are the posting granularity, behind a per-destination BAA gate;
5. each system owns its own patient record and the records are linked;
6. the patient CRUD scope (fields, delete semantics, non-goals, audit, synthetic fixtures).

Where this design goes beyond those decisions, the text says so and flags the point for owner review. Each flagged point is collected under Open Questions.

All ideation paths are relative to the root of `opensoft/openPractice-spec` and are `Status: brainstorm`, except FD-1 to FD-11, which were ratified on 2026-08-27.

Sources this design builds on:
- [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md): FD-1 to FD-11, ratified. Most used here are FD-3 (always-closed books, export-batch freeze), FD-5 (four governance tiers), FD-7 (the Thread contract before any consumer), FD-9 (open-items migration, old-system balance) and FD-11 (licensed overlay).
- [`ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md): Slices 0 to 5; risks R-2, R-6 and R-7; the open question on the accounting destination.
- [`ideation/brainstorm/openpractice-small-practice-beta-feature-normalization.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-feature-normalization.md): the party register (A-4), the six nouns, FR-1 to FR-8, and the capability register.
- [`ideation/brainstorm/openpractice-small-practice-beta-financial-control-and-accounting-bridge.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-financial-control-and-accounting-bridge.md): the subledger shape and the accounting boundary.
- [`ideation/brainstorm/openpractice-small-practice-beta-money-lines-and-bank-inbox.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-money-lines-and-bank-inbox.md): the Bank Line Inbox, the twelve kinds, and the export and certificate (§8).
- [`ideation/brainstorm/openpractice-small-practice-beta-visit-thread.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-visit-thread.md): the clinical boundary, what the Thread freezes, the forward correction loop, and the waiting-on-clinical hold.
- [`ideation/brainstorm/openpractice-feature-list-registration-and-demographics.md`](../../../ideation/brainstorm/openpractice-feature-list-registration-and-demographics.md): registration scope and the deferred identity features.
- [`ideation/brainstorm/openpractice-feature-list-interoperability-and-exchange.md`](../../../ideation/brainstorm/openpractice-feature-list-interoperability-and-exchange.md): the contracted EHR boundary, with no direct database reads.
- [`ideation/review/openpractice-feat-review-synthesis.md`](../../../ideation/review/openpractice-feat-review-synthesis.md): §2(m) "The clinical boundary has no waiting state, no correction loop, and no outage behavior", §6.7 period close, and §6.11 append-only versus deletion.

Stakeholders:
- front-desk staff and the office manager, who maintain patients;
- the biller, in-house or external (FD-1);
- the owner-physician;
- the bookkeeper or accountant, who works in the accounting system;
- the clinical staff of the linked EHR;
- privacy and security reviewers;
- adapter implementers;
- the companion openChart change, `opensoft/openChart-spec` change `define-openpractice-boundary`.

## Goals / Non-Goals

**Goals:**

- Define the administrative patient record and its maintenance precisely enough that the first Speckit feature can build it test-first.
- Make the EHR boundary bidirectional and explicit: separate records, explicit links, identity review, demographic publication and the clinical-fact feed. This closes the patient-identity half of R-7.
- Fix the billing-versus-accounting split so that openPractice never drifts into being a ledger.
- Specify one accounting contract that three destinations can implement, and build the ERPNext adapter against it in the MVP.
- Make PHI in the accounting system a gated, per-destination decision, and minimize what flows when it is allowed.
- Keep FD-3 (always-closed books, export-batch freeze) intact and destination-neutral.

**Non-Goals:**

- Duplicate scoring, a merge queue, card OCR, household modeling and the minor-to-adult transition. These are covered by the registration feature list and the normalization register.
- Deciding appointment authority (R-7).
- Building QuickBooks Online or Business Central adapters.
- Authoring the Thread contract (FD-7) or the reason taxonomy's categories.
- Asserting any vendor's BAA availability, API capability or pricing.
- Any change to another repository's runtime.

## Ownership matrix

"Source" means the authoritative owner. "Mirror" means a copy kept in step through a contract. "Reference" means an identifier pointing at the owner.

| Object or function | openPractice | EHR (openChart or another) | Accounting system |
|---|---|---|---|
| Administrative patient: name, date of birth, administrative sex, gender identity, pronouns | **Source** | Mirror via `openpractice-ehr-patient-link/1.0` | — (patient-level mode: opaque reference only) |
| Contacts and addresses | **Source** | Mirror via the same contract | — |
| Guarantor | **Source** | — (not published, minimum necessary) | Guarantor display name as customer (patient-level mode only) |
| Insurance coverage | **Source** | — (not published in 1.0) | — |
| Clinical patient anchor (for example `OC Patient`) | Reference (EHR link) | **Source** | — |
| Encounters, diagnoses, procedures, signed documentation | Reference, plus the frozen claim-as-submitted copy | **Source**; published via `openpractice-ehr-clinical-facts/1.0` | — |
| Notes, medications, results, orders | — | **Source** | — |
| Appointments | **OPEN (R-7)** | **OPEN (R-7)** | — |
| Charges, claims, remittance posting | **Source** | — | Mirror (patient-level) or aggregate (summary) |
| Patient statements and collections | **Source** | — | — |
| Payer and patient receivables, payment allocation | **Source** | — | Mirror (patient-level) or aggregate (summary) |
| Refund decisions and the refund register | **Source** (tier-4 approval) | — | Record of an issued refund |
| Financial audit lineage | **Source** | — | — |
| Reason taxonomy (the general-ledger join key) | **Source** | — | Accounts mapped per destination |
| General ledger, chart of accounts, financial statements | — | — | **Source** |
| Bank accounts and their reconciliation | Explains practice-revenue lines (see Decision 10) | — | **Source** |
| Taxes, payroll, payables | — | — | **Source** |
| Card rails and PCI scope | — (processor-hosted pages) | — | — |
| Identity linkage | Stores the EHR reference | Stores the openPractice reference | — |

## Decisions

### 1. Each system owns its own patient record; links are stored on both sides

openPractice owns the administrative patient. The EHR owns its clinical patient. Neither replaces, merges or deletes the other's record.

The link is stored on both sides using the identifier model openChart already ships: issuer or endpoint, namespace, value, status and effective interval.
- **Namespace strings.** openChart stores the openPractice patient reference under namespace **`openpractice`**. openPractice stores an openChart patient reference under namespace **`openchart`**. Both follow the product-name token convention already used by the vendored identity binding in openChart (`identifier_namespace: openchart`). A non-openChart EHR uses its own namespace token, recorded on the link.
- **Issuer.** Each openPractice deployment has a stable issuer identifier, configured at install and never derived from a host path. openChart records it in `issuer_or_endpoint`. This keeps two openPractice deployments from colliding on the same value.
- **Status.** Links use openChart's existing vocabulary: `active`, `superseded`, `revoked` and `identity_review`.

*Design call for owner review:* choosing `openpractice` and `openchart` as namespace tokens, rather than record-type-qualified forms such as `openpractice.patient`.

### 2. Patient record shape, with the sex and gender fields defined

| Attribute | Required | Definition |
|---|---|---|
| Legal name | yes (at least one part) | Family name, one or more given names, optional suffix. A single-name person is recorded without a placeholder. |
| Preferred name | no | The name the patient asks to be called; staff-facing only *(design call; not in the decision list)* |
| Date of birth | yes | Calendar date |
| **Administrative sex** | yes (`unknown` allowed) | `female`, `male`, `other` or `unknown`, aligned with FHIR R4 administrative gender. It is used for payer, eligibility and administrative transactions and is mapped to whatever code set a payer transaction requires. |
| **Gender identity** | no | Patient-reported. Coded value set plus free text, selected in Speckit planning. Never derived and never sent to payers. |
| **Pronouns** | no | Patient-reported. Never derived and never sent to payers. |
| Telephones, emails, postal addresses | no | Typed and preferred, kept as dated history |
| Guarantor | yes (defaults to self) | Exactly one current guarantor; another person is recorded in the party register |
| Coverages | zero or more | Payer, member identifier, group number, subscriber and relationship, effective dates, priority; versioned |

Sex assigned at birth, sexual orientation, preferred language, interpreter needs and confidential-communication instructions are **not** in the MVP field list. The last of these is raised as an open question, because a statement sent to the wrong address is a privacy harm (Open Question 12).

### 3. Delete is deactivate; hard delete is narrow and defers to retention control

Deactivation stops new activity and preserves everything else. Hard delete applies only to a record created in error that has no linked activity. Linked activity is defined broadly, and it explicitly includes any EHR link or accounting posting, because content already published to another system cannot be recalled from it. Hard delete is a tier-4 hard stop and leaves a non-PHI tombstone.

**Reconciliation with `add-reimbursement-retention-control`:**
- while that control is in force, every hard delete passes its disposition gate;
- an active hold always blocks a hard delete;
- audit records about the deleted record stay under retention control and survive the hard delete.

*Tension:* that change makes an unselected jurisdiction block disposition, and a gate that cannot resolve applicability refuses. A practice that has not selected a jurisdiction could therefore never hard-delete a created-in-error record. Whether "a record created in error with no linked activity" falls inside any retention record family at all is for the owner to decide (Open Question 11). Until then, the requirement fails closed.

### 4. The linkage and demographic contract: `openpractice-ehr-patient-link/1.0`

Transport is versioned REST with published schemas (Principle V), over TLS, between authenticated integration identities. Each identity is scoped by subject, tenant, purpose and role, matching openChart's ratified API requirement. Patient snapshots use a representation compatible with FHIR R4 Patient. Where FHIR R4 has no native element, as for gender identity and pronouns, the profile extension is chosen during Speckit planning and recorded in the contract schema.

**Envelope (every message):**

| Field | Meaning |
|---|---|
| `contract` | `openpractice-ehr-patient-link` |
| `contract_version` | `1.0` |
| `message_id` | Unique identifier; the idempotency key; reused on every retry |
| `message_type` | One of the types below |
| `issued_at` | UTC timestamp |
| `issuer` | The sending deployment's issuer identifier |
| `correlation_id` | Links a response to its request |
| `subject` | `openpractice_patient` {namespace `openpractice`, issuer, value}; `ehr_patient` {namespace, issuer, value} or null before linking |

**Message types:**

| Message | Direction | Content | Answer |
|---|---|---|---|
| `link.request` | openPractice to EHR | `mode` (`create_new` or `link_existing`), the demographic snapshot, the EHR patient reference for `link_existing`, the confirming actor (opaque reference) | `link.result` |
| `link.result` | EHR to openPractice | `linked` (EHR reference), `identity_review` (review reference and reason) or `refused` (reason) | — |
| `demographics.updated` | openPractice to EHR | `demographic_version`, `changed_fields`, `identity_significant`, `administrative_status`, `provenance`, full `snapshot` | `demographics.ack` |
| `demographics.ack` | EHR to openPractice | `applied`, `duplicate`, `stale`, `rejected` (with reason) or `identity_review` | — |
| `link.review_resolved` | raising side to the other | `confirmed` or `revoked`, actor, reason | acknowledgement |
| `link.revoked` | either side | Link ended; both sides keep the link record with status `revoked` | acknowledgement |

**Versioning and ordering:**
- `demographic_version` starts at 1 when the record is created and increases on every published change.
- Snapshots are complete, so receivers do not need contiguous versions and out-of-order delivery is safe: a lower version is `stale`.
- The same version with different content is `rejected`. This signals an integrity defect and creates work.
- MINOR versions add optional fields only. A receiver rejects a MAJOR version it does not support.

**Idempotency and delivery:**
- The outbox row is written in the same transaction as the demographic change. This mirrors the outbox pattern in `add-reimbursement-retention-control`.
- Delivery is asynchronous, with backoff.
- Retries reuse `message_id`.
- An exhausted retry policy produces work owned by a Roster entry.
- An EHR outage never blocks registration.

**Identity review.** A conflicting link enters `identity_review`, and resolution never merges. On the openChart side, `define-openpractice-boundary` adds two further holds:
- a `link_existing` request whose demographics disagree with openChart's record goes to identity review;
- an applied update that changes date of birth is held for review before it takes effect. This guards against propagating demographics across a wrong-patient link.

*Design calls for owner review:*
- guarantor and coverage are excluded from publication on minimum-necessary grounds;
- the deactivation reason is excluded and only the status is published;
- the hold on date-of-birth changes on the EHR side;
- tier 4 for `link_existing`.

### 5. The clinical-fact contract: `openpractice-ehr-clinical-facts/1.0`

The EHR produces this contract and openPractice consumes it. It has two parts:
- **Pointer-only notifications**, carrying resource references and no clinical content: `encounter.documentation_final`, `clinical_fact.corrected` and `clinical_fact.entered_in_error`.
- **Authorized FHIR R4-compatible reads**, limited to linked patients and the billing purpose:
  - Encounter: identifier, status, type, period, location and participating practitioners;
  - Condition, encounter-diagnosis category: code system, code, rank and encounter reference;
  - Procedure: code system, code, quantity where documented, performed time, performer and encounter reference;
  - a documentation-final indicator with signer reference and signed time.

Note text, medication lists, results and orders are out of scope.

Coding follows FD-11. The contract carries code-system identifiers and code values only, never licensed descriptor text. Fixtures use license-free or synthetic codes, so no CPT content enters the repository. Modifier and unit derivation stays with openPractice charge capture unless the EHR documents them.

A correction is a new version that references the version it supersedes. A retraction is explicit. openPractice turns either one into a dated event and a corrected-claim action; it never rewinds the Thread.

*Dependency:* openChart has no clinician-authored encounter, diagnosis or procedure records today. Its intake capability holds patient-reported statements only, and its ratified spec forbids treating those as diagnoses. openChart can therefore publish no billable facts until a governed clinical-encounter capability exists in openChart. The companion change records the same dependency.

### 6. Billing versus accounting

openPractice owns claims, statements, payer and patient receivables, allocation, refunds, collections and financial audit lineage. It is the receivable source of truth. The accounting system owns the general ledger, chart of accounts, bank accounts, taxes, payroll, payables and the final statements, and it **mirrors** what openPractice posts. A destination-side edit is a reconciliation exception, never truth. This sharpens the boundary already drawn in the financial-control brainstorm ("openPractice remains the source for healthcare receivable lineage. The connected accounting system remains the source for the general ledger ...") and in the money-lines brainstorm ("It is not an accounting system").

FD-3 holds unchanged, except that its export-batch freeze now applies to every destination: immutability at post time, dated reversals, as-of queries, no openPractice period close, and frozen batches with back-referenced corrections. When the destination closes a period, a late correction posts to the destination's open period with a back-reference.

### 7. The accounting contract: `openpractice-accounting-integration/1.0`

| Operation | Purpose | Modes |
|---|---|---|
| `customer.upsert` | Create or update a guarantor-account customer or a payer customer | patient-level |
| `invoice.post` | Raise a receivable against a customer | patient-level |
| `payment.post` | Record a receipt into the clearing account and apply it to invoices | patient-level |
| `credit_note.post` | Reduce a receivable: contractual adjustment, responsibility transfer, write-off | patient-level |
| `reversal.post` | Dated reversal of a previously posted document, with a back-reference | both |
| `refund.post` | Record an issued refund against a customer credit | patient-level |
| `deposit.post` | Move an explained bank line's amount from the clearing account to the bank account | patient-level |
| `journal.post` | Account-level journal entry: summary-mode deposit or day entries, and non-customer items such as fees, unidentified receipts and interest | both |
| `batch.export` | Deliver a released batch | both |
| `reconciliation.report` | Read destination totals for keyed documents and compare them with openPractice | both |

- **Idempotency key:** `<destination_id>:<operation>:<source_entry_id>:<source_entry_version>`. After an indeterminate outcome, look the entry up by its key before any retry.
- **Batch identity:** a sequence per destination, the contract version, the mapping version, the posting mode, the releasing actor, the release time and a SHA-256 content digest over the canonical content. Content is frozen at release; delivery state is tracked per entry, separately from content.
- **Release:** an explicit action by an authorized person. In practice it is expected to sit in the End-of-Day Close or the bookkeeper's routine. Scheduled automatic release is off by default (Principle II), which also keeps this change clear of R-2.
- **Release refusals:** an unmapped reason category; patient-level mode without a satisfied BAA gate; a deposit that cites an unexplained bank line.
- **Entry states:** `pending`, `posted` (destination document reference recorded), `failed_transient`, `failed_permanent`, `indeterminate` and `superseded`.
  - Transient failures retry with backoff and the same key.
  - Indeterminate outcomes are resolved by lookup.
  - Permanent failures become work. They are resolved either by redelivering the unchanged entry once the destination-side cause is fixed, or by a superseding correction in a later batch.
- **Reconciliation report:** produced after each delivery and on demand as of any date. It compares totals by reason category, and by customer in patient-level mode. It lists missing, unexpected, mismatched and destination-edited documents. It states its exclusions: unidentified receipts, "not ours to explain" lines and old-system collections. It is the successor to the brainstorm's one-page reconciliation certificate.

### 8. Posting modes and the BAA gate

Posting mode is a per-destination setting, and each batch records the mode it was released under.
- **`patient_level`** is the intended default granularity. It requires a verified BAA. Enabling it is a tier-4 hard stop.
- **`summary`** carries no PHI. It posts one journal entry per explained bank deposit, or per day, split by reason category, with fees and refunds as their own lines. This is the brainstorm's shape, kept as the fallback.

A destination is created in `summary` mode and moves to `patient_level` only through the gated act. A mode change takes effect at a batch boundary.

The BAA record captures the counterparty, the execution date, effective and renewal or expiry dates, a scope statement covering the accounting service, an owner-local document reference, the verifying person and the verification time. When the BAA expires or is revoked:
- patient-level delivery halts;
- pending patient-level work is held, not converted;
- an authorized person receives the work.

This change makes **no claim** about whether ERPNext's hosting providers, Intuit or Microsoft offer or will sign a BAA. That must be verified per vendor and per hosting arrangement before patient-level mode is enabled. The gate consumes the "BAA-Gated Vendor Activation" capability that the normalization register places in Slice 0.

### 9. Proposed posting map and customer model (flagged for owner review)

**Customer model proposal:**
- The accounting customer for patient receivables is the **guarantor account**, not the patient. A guarantor who covers several patients is one customer, and a minor's balance is not booked in the minor's name.
- Each **payer is a customer** for insurance receivables. Payer names are not PHI.
- Invoices carry an opaque openPractice patient or visit reference, and **omit the patient's name by default**, as a minimum-necessary choice.
- *Alternative:* the patient as customer. This is simpler to explain, but it splits a family's balance, books minors' debts in their own names, and exposes every patient's name in the books.

**Proposed posting map:**

| openPractice event | Patient-level posting | Summary-mode posting |
|---|---|---|
| Claim-billed visit's charges finalized (`charged`) | `invoice.post` to the payer customer | — |
| Self-pay visit's charges finalized | `invoice.post` to the guarantor customer | — |
| Contractual adjustment at remittance | `credit_note.post` on the payer invoice | — |
| Patient responsibility determined | `credit_note.post` on the payer invoice, plus `invoice.post` to the guarantor, linked by source event | — |
| Payer or patient payment posted | `payment.post` into clearing, applied to the invoice | — |
| Unidentified receipt | `journal.post` to an unidentified-receipts liability account, no customer | Liability line in the deposit entry |
| Explained bank line | `deposit.post`, clearing to bank, equal to the bank line | One `journal.post` per explained bank line |
| Merchant or bank fee | `journal.post` to fee expense | Fee line in the deposit entry |
| Refund issued | `refund.post` | Refund line when its bank debit is explained |
| Write-off | `credit_note.post` to the write-off reason | — |
| Any correction | `reversal.post` with back-reference | Reversing `journal.post` with back-reference |
| Old-system collection | Not posted at patient level | Bookkeeper's decision (open) |

*Consequence to note:* posting invoices at `charged` puts receivables in the books on an accrual footing, which affects the brainstorm's "cash basis by default". Whether a practice can still report on a cash basis depends on each destination's reporting and must be verified per destination (Open Question 5). The timing proposal (`charged` rather than claim submission) is flagged too.

### 10. Bank reconciliation ownership: brainstorm position kept, tension flagged

The money-lines brainstorm has openPractice explain every practice-revenue bank line in the Bank Line Inbox, and makes "explained" the precondition for the export. Decision 2 makes the accounting system the owner of bank accounts. This design keeps the brainstorm's position:
- openPractice explains practice-revenue lines and posts a deposit only for an explained line;
- the accounting system owns the bank account and its reconciliation;
- a line dispositioned "not ours to explain" never reaches the destination.

The clearing-account pattern in Decision 9 preserves the brainstorm's key property: one deposit entry per bank line, so the bookkeeper's own bank reconciliation matches it in one step.

**The tension is not resolved here.**
- The bank statement is now read by two systems (openPractice's inbox and the accounting system's bank reconciliation).
- The brainstorm's valve, "explained is the export's precondition", applies only to deposits under patient-level mode. Invoices and credit notes post before any bank line exists.

Whether openPractice should instead read bank lines from the destination through the adapter, rather than from its own statement upload, is Open Question 6.

### 11. PHI minimization

- **Summary mode:** no element that identifies an individual or relates a posting to one.
- **Patient-level mode, allowed:**
  - guarantor display name and an opaque guarantor account reference;
  - payer name and identifier;
  - opaque patient, visit, claim and entry references;
  - amounts, posting dates and invoice dates;
  - reason-category descriptions.
- **Patient-level mode, never sent:**
  - date of birth, address, telephone or email;
  - government identifiers, member or group identifiers, payer claim control numbers;
  - diagnosis or procedure codes or descriptions, service descriptions beyond the reason category, dates of service;
  - any clinical content.
- **The EHR link:** demographics only. No guarantor, coverage, financial data or deactivation reason.
- **Clinical facts:** the billing subset only. No notes, medications, results or orders.
- **Everywhere:** no PHI in logs, fixtures, examples or commits.

### 12. Adapter notes

**ERPNext Accounts (MVP).**
- The expected mapping is to ERPNext's Customer, Sales Invoice, return Sales Invoice (credit note), Payment Entry and Journal Entry documents, against the company's chart of accounts. This must be verified against the ERPNext major version pinned during Speckit planning.
- Two topologies are supported:
  - **Same site or bench.** The adapter calls ERPNext's documented document interface. openPractice and ERPNext must share a Frappe major version.
  - **Remote, over TLS.** The adapter uses ERPNext's documented REST interface, which decouples the versions.
- Never SQL and never direct table writes.
- The mechanism for carrying the idempotency key on ERPNext documents is a planning decision. Options are a supported customization field installed by the adapter, or an existing reference field. It must not require direct table writes.
- Packaging, as a separately installable Frappe app or a lazily loaded module, is also a planning decision. The requirement is that core has no import-time dependency on ERPNext.
- *Open:* licensing review of same-site in-process calls into ERPNext; see Open Question 9.

**QuickBooks Online and Microsoft Dynamics 365 Business Central (post-MVP; contract level only).** Each adapter must:
- map every contract operation to the destination's receivable, payment, credit and journal documents;
- provide idempotent creation, natively or by an external-reference lookup;
- support the reconciliation read;
- keep credentials least-privileged, with no payment-origination permission where it is separable;
- pass the same contract tests.

Before either adapter's feature is planned, the following must be verified against the vendor's current documentation and contract terms: authentication model, API surface and object model, rate limits, idempotency support, data residency, the ability to report on a cash basis, and BAA availability. **This change asserts none of them.**

These two services are proprietary single-vendor cloud services. Under Principle VI they are optional adapters behind configuration. openPractice core never depends on them, and a practice can run entirely on a self-hosted ERPNext.

### 13. Governance tiers (FD-5): proposed, for owner review

| Action | Proposed tier |
|---|---|
| Create patient | 1 — do + undo |
| Edit demographics, contacts or addresses | 2 — do + notify (the normalization register puts "contact changes" at tier 2) |
| Add or change coverage or guarantor | 2 |
| Deactivate or reactivate | 2 |
| Hard delete a record created in error | 4 — hard stop (it changes record identity) |
| EHR link, `create_new` | 2 |
| EHR link, `link_existing`; resolve identity review | 4 |
| Revoke an EHR link | 2 |
| Demographic publication | No separate gate; an automatic consequence of the human edit |
| Change an account mapping | 2 |
| Release an export batch | A person's explicit action (tier 2: the release is the approval), not a tier-3 countdown, while R-2 is unresolved |
| Enable patient-level mode | 4 |
| Switch a destination to summary mode | 2 (it reduces PHI) |

### 14. Handoff, ratification and closure

This change mirrors `add-reimbursement-retention-control`:
- `Status: draft`, which only the owner changes;
- strict validation;
- a durable unconditional owner ratification in `owner-ratification.md`;
- feature creation blocked until ratification;
- archival blocked until the implementing work lands and is accepted (`closure-evidence.md`).

**Handoff-shape tension.** The global workflow protocol (Lifecycle step 3) says an OpenSpec change "hands off to exactly one Speckit feature". `tasks.md` lists an ordered sequence of features, because the four capabilities ship in different slices (0 and 4) and have different prerequisites (FD-7's Thread contract, the money ledger). `owner-ratification.md` therefore asks the owner to choose explicitly:
- **(A)** ratify the ordered multi-feature handoff as an exception for this change; or
- **(B)** split this change into per-capability changes before any feature is created.

## Constitution Check (pre-handoff, against v1.0.0)

| Principle or gate | Obligation this change creates | Status |
|---|---|---|
| I. HIPAA by design | Registry audits every change and every PHI read. PHI is excluded from logs and fixtures. Encryption in transit and at rest covers the registry, outbox, batches and BAA documents. Access is minimum-necessary, derived from the Roster. Session controls are platform-level and carried to each feature's plan. | Satisfied at requirement level; per-feature safeguard plans owed |
| II. Security review | Every feature touches PHI, money or credentials and needs a documented security review and a risk-analysis update. Each destination's patient-level enablement needs its own risk entry. Batch release is explicit, refunds are tier 4, and no autonomous financial action is on by default. | Owed per feature; R-2 avoided, not resolved |
| III. Test-first | Contract tests at the EHR boundary (openChart is named in the principle) and at the accounting boundary ("every integration boundary"). Calculation tests for the posting map, with boundary and failure cases. Synthetic fixtures and contract doubles only. | Owed per feature |
| IV. Clinical boundary and originality | openPractice keeps references and claim-as-submitted copies only. No-EHR charge entry is administrative data. The ERPNext adapter is original code that calls documented interfaces. | Satisfied; licensing question raised (Open Question 9) |
| V. Versioned contracts only | Three named, versioned contracts. FHIR R4-compatible for patient and clinical facts. Versioned REST for accounting. No direct database access in either direction. | Satisfied; same-site in-process reading raised (Open Question 9) |
| VI. Portability | ERPNext is self-hostable. QuickBooks Online and Business Central are optional adapters only. Environment-specific behavior lives in configuration. | Satisfied |
| Data gate | Synthetic-only fixtures; no credentials in any commit | Satisfied at requirement level |

## Conflicts and tensions with ratified or governing text

Each tension is recorded rather than silently decided.

- **T-1: Workflow protocol, "exactly one Speckit feature".** See Decision 14. The owner must choose A or B at ratification.
- **T-2: FD-7, the Thread contract before any consumer.** Accounting postings derive from ledger entries that reference Threads. `tasks.md` therefore blocks the accounting-adapter feature until the Thread contract is versioned. The patient registry and the EHR link do not consume the Thread, because the party register is substrate the Thread presupposes.
- **T-3: FD-3's wording, "exported to QuickBooks".** Generalized to any destination. The substance is kept.
- **T-4: Money-lines brainstorm (non-normative).**
  - The deposit-level, cash-basis default is superseded by patient-level invoices.
  - The rule that only explained lines reach the export now governs deposits only.
  - Old-system collections remain excluded.
- **T-5: Bank reconciliation is done by two systems.** See Decision 10. Not decided.
- **T-6: R-2, governance tier 3 versus Principle II.** This change keeps accounting release explicit, so it neither needs nor supplies the reading R-2 asks for.
- **T-7: The retention change and hard delete.** An unselected jurisdiction could block every hard delete. See Decision 3 and Open Question 11.
- **T-8: Principle V and same-site ERPNext.** It is unclear whether in-process calls to ERPNext's document interface count as "direct database access". This change prohibits SQL and table writes, but the reading is the owner's (Open Question 9).
- **T-9: The BAA gate as worded.** It requires "an executed HIPAA BAA with the destination's vendor". For a destination the practice operates itself, with no vendor, the gate as written cannot be satisfied (Open Question 7).
- **T-10: openChart cannot yet supply billable facts.** See Decision 5. Charge capture against openChart waits on a separate openChart capability.
- **T-11: Interoperability brainstorm direction.** That brainstorm has openPractice consuming Patient and Coverage from openChart. Decision 5 reverses that for administrative data.
- **T-12: Scope-and-sequencing, "first staged proposal".** This change is staged ahead of the vertical-thread proposal. Recorded in `proposal.md`.

## Risks / Trade-offs

- **[PHI in a third party's books widens the breach surface and the business-associate footprint]** → Gate patient-level mode on a verified BAA per destination, at tier 4. Minimize the fields sent. Halt delivery on BAA lapse. Give each destination its own risk-analysis entry.
- **[Duplicate or lost postings across retries]** → Deterministic idempotency keys, lookup before retry on indeterminate outcomes, and per-entry delivery state separate from frozen content.
- **[The bookkeeper edits mirrored documents]** → The reconciliation report flags destination-side edits as exceptions. openPractice never imports them.
- **[A wrong-patient link pushes demographics onto the wrong clinical record]** → `link_existing` is human-confirmed at tier 4. openChart checks that demographics agree before linking and holds date-of-birth changes for review. Conflicts never merge.
- **[EHR outage]** → The outbox queues demographic updates. Charge capture waits in waiting-on-clinical with the filing-window clock. Registration never blocks.
- **[The contracts drift between repositories]** → Identical contract names, versions and namespaces in both changes. Contract tests on both sides. MAJOR-version rejection.
- **[Vendor API, terms or BAA posture change]** → Adapters sit behind one contract, and a destination can fall back to summary mode by explicit action.
- **[Same-site ERPNext couples Frappe versions and raises a licensing question]** → The remote topology is supported. The owner's reading and a counsel check come before same-site in-process calls ship.
- **[Two systems reconcile the same bank account]** → Decision 10 keeps one deposit per explained line, and Open Question 6 asks whether to read bank lines from the destination.
- **[A multi-feature handoff departs from protocol]** → The owner chooses A or B explicitly at ratification.

## Migration Plan

1. Strictly validate this change. The owner ratifies the exact revision unconditionally, choosing handoff shape A or B.
2. Create the patient-registry feature first (Slice 0): fields, coverage, guarantor, search, deactivation, hard delete, audit, and synthetic fixtures under test-first and security review.
3. Create the EHR patient-link feature (Slice 0): link requests, outbox, acknowledgements, identity review, and contract tests against an openChart double. Live interoperation waits for openChart's `define-openpractice-boundary` work.
4. Define the reason taxonomy and immutable money ledger (Slice 0, separately governed). Version the Thread contract (FD-7, separately governed).
5. Create the accounting-contract and ERPNext-adapter feature (Slice 4) after step 4: contract, delivery engine, batches, release, reconciliation, posting modes, BAA gate, the ERPNext adapter in both topologies, and contract tests. Patient-level mode is enabled per destination only after its BAA gate and risk entry.
6. Consume clinical facts with charge capture (Slice 3), once a producing EHR capability exists.
7. QuickBooks Online and Business Central adapters follow after the MVP, each after vendor verification, against `openpractice-accounting-integration/1.x`.
8. Roll back by disabling a destination or link. Undelivered work is held, and released batches, ledger history, audit and outbox records are preserved. Never roll back by deleting postings or audit records.
9. Archive only after the implementing features and the root pin bump have landed and `closure-evidence.md` is complete.

## Open Questions

Items marked ⚑ are design calls this change made beyond Brett Heap's decisions. They need owner review.

1. **Appointment authority (R-7).** Which system is authoritative for the appointment, and what happens during an EHR outage? Deliberately undecided.
2. **Handoff shape (T-1).** A multi-feature exception, or a split into per-capability changes?
3. ⚑ **Accounting customer.** The guarantor account (proposed) or the patient? Payers as customers (proposed)?
4. ⚑ **Patient name on invoices.** Omitted by default (proposed). Should it be included for bookkeeper usability?
5. ⚑ **Invoice timing and cash basis.** Post invoices at `charged` (proposed) or at claim submission? Can each destination still report on a cash basis? Verify per destination.
6. **Bank lines (T-5).** Keep openPractice's own statement upload, or read bank transactions from the destination through the adapter?
7. **BAA gate for a practice-operated destination (T-9).** Proposal for the owner: allow an attestation that names the operator, where the operator is the practice itself or an existing business associate whose BAA covers the accounting site. Until ruled, the gate fails closed.
8. ⚑ **Switching mode mid-stream.** How are opening customer balances established when a destination moves from summary to patient-level, and how is continuity kept when it moves back?
9. **Same-site ERPNext (T-8).** Do in-process document-interface calls satisfy Principle V? Does in-process use of ERPNext (distributed under GPL-3.0) from Apache-2.0 openPractice code raise a licensing issue? Counsel verification is needed before same-site in-process calls ship.
10. **Frappe version policy** for same-site ERPNext co-installation.
11. **Hard delete versus retention (T-7).** Is "created in error with no linked activity" inside any retention record family? If not, does the unselected-jurisdiction block apply to it?
12. ⚑ **Confidential communications.** Should the MVP registry carry confidential-communication instructions (alternate address or channel) before statements ship in Slice 2?
13. ⚑ **Fields left out.** Sex assigned at birth, sexual orientation and preferred language are excluded from the MVP field list. Preferred name is included. Confirm.
14. ⚑ **Coverage to the EHR.** Coverage is excluded from the 1.0 link contract. Add it later as a MINOR?
15. ⚑ **Date-of-birth review hold** on the EHR side, and tier 4 for `link_existing`. Confirm.
16. ⚑ **Governance tiers** in Decision 13. Confirm or amend.
17. **Old-system collections.** Where do legacy collections post? (Carried from the money-lines brainstorm; the bookkeeper's decision.)
18. ⚑ **Namespace tokens** `openpractice` and `openchart` (Decision 1). Confirm.
