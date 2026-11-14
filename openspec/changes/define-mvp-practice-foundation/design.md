## Context

openPractice is an assembly root whose code leg is empty. The Frappe application `open_practice` does not exist yet. Constitution v1.0.0 (`.specify/memory/constitution.md` in `opensoft/openPractice`, ratified 2026-08-24) is in force. Its principles are:

- I: HIPAA by design
- II: security review for PHI, money, authorization and credentials
- III: test-first development, with contract tests at every integration boundary
- IV: clinical boundary and original implementation
- V: versioned integration contracts only
- VI: deployment portability

The product owner, Brett Heap, made six boundary decisions on 2026-11-14. Later the same day he ruled on 24 follow-up questions; see "Owner rulings (2026-11-14)" below. **Rulings are not ratification.** This change stays `Status: draft` and `owner-ratification.md` stays PENDING until the owner completes it.

Requirement IDs (`PR-nn`, `EB-nn`, `BAB-nn`, `AI-nn`) prefix every requirement name in the four spec deltas.

Ideation paths are relative to the root of `opensoft/openPractice-spec`. All of those documents are `Status: brainstorm`, except FD-1 to FD-11, which were ratified on 2026-08-27. Sources:

- [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md): FD-1 to FD-11 (ratified). The most relevant are FD-2 (learned baseline), FD-3 (always-closed books and export-batch freeze), FD-5 (four tiers and deadline escapes), FD-7 (Thread contract first), FD-9 (old-system balance) and FD-11 (licensed overlay).
- [`ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md): Slices 0–5 and risks R-2, R-6 and R-7.
- [`ideation/brainstorm/openpractice-small-practice-beta-feature-normalization.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-feature-normalization.md): the party register (A-4), the six nouns, FR-1 to FR-8, and the capability register.
- [`ideation/brainstorm/openpractice-small-practice-beta-financial-control-and-accounting-bridge.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-financial-control-and-accounting-bridge.md): the subledger and the accounting boundary.
- [`ideation/brainstorm/openpractice-small-practice-beta-money-lines-and-bank-inbox.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-money-lines-and-bank-inbox.md): the bank-line inbox, the twelve kinds, and the export (§8).
- [`ideation/brainstorm/openpractice-small-practice-beta-visit-thread.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-visit-thread.md): the clinical boundary, what a thread freezes, forward corrections, and waiting-on-clinical.
- [`ideation/brainstorm/openpractice-small-practice-beta-enrollment-gated-billing.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-enrollment-gated-billing.md) and [`ideation/brainstorm/openpractice-small-practice-beta-provider-enrollment-and-directory.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-provider-enrollment-and-directory.md): the participation tuple and enrollment cases.
- [`ideation/brainstorm/openpractice-feature-list-registration-and-demographics.md`](../../../ideation/brainstorm/openpractice-feature-list-registration-and-demographics.md) and [`ideation/brainstorm/openpractice-feature-list-interoperability-and-exchange.md`](../../../ideation/brainstorm/openpractice-feature-list-interoperability-and-exchange.md).
- [`ideation/review/openpractice-feat-review-synthesis.md`](../../../ideation/review/openpractice-feat-review-synthesis.md): §2(m) clinical boundary, §6.7 period close, and §6.11 append-only integrity versus deletion.
- Brainstorm packet `openpractice-customer-financial-separation`, cited by topic anchor only; in progress, to be linked when it lands. It covers how membership, subscription and retail invoicing are routed and owned, separately from medical billing (ruling 24).

## Owner rulings (2026-11-14)

Every row has the same source: **Brett Heap, 2026-11-14, authoring session.** The requirement text matches each ruling.

| # | Ruling | Governs |
|---|---|---|
| 1 | Handoff shape **A**: an ordered multi-feature exception. Pre-stated as the owner's ruling; the field in `owner-ratification.md` is left for him to confirm. | `tasks.md` §1.2, §2 |
| 2 | Accounting customers are the guarantor account (patient balances) and each payer (insurance balances). | AI-10 |
| 3 | A patient-balance customer is identified by account number only. No guarantor or patient name ever reaches the destination, on customers or invoice lines. With guarantor = self, a guarantor-named customer would be the patient's name. | AI-10, AI-16, PR-06 |
| 4 | Dates: line date of service; GL date = date of service if that period is open, otherwise the first open date with a late-charge flag (FD-3); invoice date = finalization date; due date from statement terms; sync timestamp kept separately and never an accounting date; each adapter verifies line service dates and cash-basis support before go-live. | AI-11, AI-07, AI-20, BAB-15 |
| 5 | Banks: a register of all practice accounts; payer deposit links per payer × billing entity (TIN + NPI) with method, account, enrollment status and evidence; an expected-account check; a re-enrollment worklist; these feed the enrollment lifecycle. openPractice reconciles deposits only, reading revenue bank lines from the destination with statement upload as fallback. Other lines are labelled "accounting system's". One deposit entry per explained deposit. The accounting system owns bank balances and the full reconciliation. | BAB-02, BAB-09, BAB-11, BAB-12, BAB-13, BAB-14, AI-17 |
| 6 | A practice-operated destination (for example self-hosted ERPNext) satisfies the BAA gate with an attestation naming the operator. | AI-14 |
| 7 | Posting mode is chosen before a destination's first released batch. Every destination starts in summary and may move to patient-level only before the first release, after the BAA gate. After that, any mode change is refused until the owner ratifies a continuity procedure. Mapping is versioned per destination and mode. The "fall back to summary" mitigation is dropped. A BAA lapse halts delivery with no silent conversion. | AI-09, BAB-06, AI-14 |
| 8 | The MVP calls ERPNext only through its REST API, same-site or remote, with no in-process calls. In-process use stays conditional on counsel. The remote topology alone satisfies MVP acceptance. | AI-04, `tasks.md` §2.3 |
| 9 | A record created in error with no linked activity is outside every retention record family. Hard delete is tier 4 and removes the live content and the PHI values in that record's audit trail, leaving a PHI-free tombstone. Backups age out under backup retention. No unscoped "permanent" claim. `add-reimbursement-retention-control` must carry this exclusion when ratified. | PR-12, PR-13, PR-10 |
| 10 | Confidential-communication instructions are in the MVP registry. | PR-05, EB-06 |
| 11 | Preferred language is added. Preferred name stays. Sex assigned at birth and sexual orientation stay out (clinical; owned by openChart). | PR-03, EB-06 |
| 12 | Tier 4 applies only to hard delete and to enabling patient-level mode, as a conscious FD-5 exception (T-13). `link_existing` and review resolution are tier 2. The FD-5 deadline escape is stated for gates. | PR-12, AI-14, EB-04, EB-05, EB-18 |
| 13 | Coverage is not in link contract 1.0; it may come in a later MINOR. | EB-06 |
| 14 | The date-of-birth hold is accepted, implemented as a link-level suspension. | EB-05, EB-08 (openChart PL-08) |
| 15 | The "agree" check for `link_existing` is accepted and made deterministic against the built DocType. | EB-05 (openChart PL-05) |
| 16 | The applied legal name and date of birth are projected onto `OC Patient`, with history. `sex_recorded` is never overwritten. | openChart PL-09 |
| 17 | openChart refuses direct edits to a linked patient's projected fields and routes them to openPractice as correction requests; intake flows included. | EB-09 (openChart PL-10) |
| 18 | The openChart integration role is named and scoped in planning, narrower than System Manager. | openChart PL-05 |
| 19 | Namespace tokens `openpractice` and `openchart` are confirmed. | EB-01 |
| 20 | Coverages are zero or more; self-pay patients have none. | PR-07, `proposal.md` |
| 21 | Manual charge entry is allowed for a linked patient while that EHR reports no billable-facts capability. It is marked manual, with provenance. | EB-17 |
| 22 | Accrual valuation: post the billed charge plus an expected contractual adjustment at the date of service, from the payer's observed history (FD-2), and true up at remittance. Fall back to gross at date of service, with the adjustment at remittance, when no history exists. | AI-12 |
| 23 | Full bank account and routing numbers are stored encrypted, visible only to owner and billing-admin roles, masked elsewhere, every view audited, and never in logs, errors, exports, postings or keys. | BAB-10, AI-16, AI-19 |
| 24 | Membership agreements, subscription renewals, retail sales and other non-medical commerce invoicing are out of scope. Their routing and ownership are being worked out in the `openpractice-customer-financial-separation` brainstorm packet (in progress; to be linked when it lands). Ruling 3 is scoped to customers created by this contract from medical billing: guarantor accounts (including self-pay and direct healthcare charges under FD-4) and payers. It does not govern membership or retail customer records. The contract never copies care linkage or PHI into a customer record it does not own, and never posts membership or retail obligations. How the two kinds of receivable coexist in one destination is an open question. | AI-10, AI-16, AI-22, `proposal.md` non-goals, Open Question 7 |

## Goals / Non-Goals

**Goals**

- Specify the administrative patient record precisely enough that the first feature can be built test-first.
- Make the EHR boundary bidirectional and explicit: links, issuers, single-resolver identity review, demographic publication, correction requests and clinical facts. This closes the patient-identity half of R-7.
- Fix the split between billing and accounting so that openPractice never becomes a ledger.
- Specify one accounting contract for three destinations, and build the ERPNext adapter in the MVP.
- Gate PHI in the accounting system per destination, and minimize what is sent.

**Non-Goals**

- Duplicate scoring, a merge queue, card OCR and household modeling.
- Deciding appointment authority (R-7).
- QuickBooks Online and Business Central adapters.
- Authoring the Thread contract or the reason taxonomy.
- Membership agreements, subscription renewals, retail sales and other non-medical commerce invoicing (ruling 24; `openpractice-customer-financial-separation`, in progress).
- Making vendor BAA or API claims.
- Changing another repository's runtime.

## Ownership matrix

| Object or function | openPractice | EHR | Accounting system |
|---|---|---|---|
| Administrative demographics, contacts, confidential-communication instructions, preferred language | **Source** | Mirror (link contract) | — |
| Guarantor, coverage | **Source** | — | Guarantor account number as customer (patient-level only) |
| Clinical patient anchor; encounters, diagnoses, procedures, documentation | References and claim-as-submitted copies | **Source** | — |
| Notes, medications, results, orders | — | **Source** | — |
| Appointments | **OPEN (R-7)** | **OPEN (R-7)** | — |
| Charges, claims, statements, collections, payer and patient receivables, allocation, refunds, financial audit lineage | **Source** | — | Mirror (patient-level) or aggregate (summary) |
| Reason taxonomy | **Source** | — | Accounts mapped per destination and mode |
| Practice bank account register; payer deposit links | **Source** | — | Mapped bank accounts |
| Revenue bank-line explanation (deposits, chargebacks, takebacks, processor fees) | **Owner** | — | Supplies the lines |
| Bank balances and full bank reconciliation | — | — | **Source** |
| General ledger, chart of accounts, taxes, payroll, payables, financial statements | — | — | **Source** |
| Identity linkage | Stores (EHR issuer, namespace, value) | Stores (openPractice issuer, `openpractice`, value) | — |
| Membership, subscription and retail invoicing, with their named commerce customers | **Out of scope** (ruling 24): never posted by this contract, which never copies care linkage into these records | — | Governed elsewhere (`openpractice-customer-financial-separation`, in progress) |

## Decisions

### 1. Separate records, linked by triples with configured issuers

Each side stores the other side's reference as an (issuer, namespace, value) triple and compares references as triples (EB-01, EB-02). Every openPractice deployment and every openChart deployment configures a stable issuer that is unique across deployments. This is why two openChart sites that both have `OC-PAT-00001` never collide.

Namespaces are `openpractice` and `openchart` (ruling 19), following the product-name convention already in openChart's vendored identity binding. Every message carries a `link_id`, minted by openPractice, that names one link for its whole life. Version comparisons are made per `link_id` (EB-07).

### 2. The patient record

| Attribute | Rule |
|---|---|
| Legal name | At least one part. A single-name person is recorded without a placeholder. |
| Preferred name; preferred language (BCP 47) | Optional (ruling 11) |
| Date of birth | Required |
| Administrative sex | `female`, `male`, `other` or `unknown`. Used for payer and administrative transactions. |
| Gender identity; pronouns | Optional and patient-reported. Never derived. Never sent to payers. |
| Contacts and addresses | Typed, preferred, kept as dated history |
| Confidential-communication instructions | Alternate address and channel, plus the coded restrictions in PR-05 (ruling 10). Every contact function must honor them. |
| Guarantor | Exactly one current guarantor, defaulting to self. Each has an opaque guarantor account number. |
| Coverages | Zero or more (ruling 20), prioritized and versioned |
| Not carried | Sex assigned at birth and sexual orientation (clinical; ruling 11) |

### 3. Delete, deactivate and hard delete

Deactivation (PR-11) refuses only activity dated after the deactivation date. Services already given, including to a patient who has since died, stay chargeable. The wording is neutral about appointment authority (R-7).

Hard delete (PR-12, PR-13) applies only to a record created in error with no linked activity. Such records are outside every retention record family (ruling 9). Hard delete is tier 4 and removes the live content and the PHI values in that record's audit trail, leaving a PHI-free tombstone. Backup copies age out under backup retention, and no unscoped "permanent" claim is made.

**Dependency.** When `add-reimbursement-retention-control` is ratified, it must carry this exclusion. This is a dependency note only; this change does not edit that one.

**New tension T-14.** PR-10 makes audit records tamper-evident and editable by no one, but PR-13 removes PHI values from one record's audit trail. The design that satisfies both keeps audit values outside the tamper-evident chain, which records only a keyed commitment to each value. The sanctioned removal deletes the value and its key, and appends a redaction event. The Speckit plan must choose the mechanism.

### 4. The patient-link contract: `openpractice-ehr-patient-link/1.0`

The canonical schema is `contracts/openpractice-ehr-patient-link-1.0.schema.json`, SHA-256 `76f465e144893000de4bd2240adb9f547899e2e8100315f698c3cc6cb885b802`. Transport is versioned REST over TLS between integration identities scoped by subject, tenant, purpose and role. The snapshot is compatible with FHIR R4 Patient.

| Message | Direction | Purpose |
|---|---|---|
| `link.request` | openPractice → EHR | Mode `create_new` or `link_existing`, with snapshot and version. Mints the `link_id`. |
| `link.result` | EHR → openPractice | `linked`, `identity_review` or `refused`, with a coded reason |
| `demographics.updated` | openPractice → EHR | Full snapshot, `demographic_version`, changed fields, coded provenance |
| `demographics.ack` | EHR → openPractice | `applied`, `duplicate` (same message, or same version with identical content), `stale`, `rejected` (content conflict, unsupported MAJOR, or revoked, superseded or unknown link) or `identity_review` |
| `demographics.hold_rejected` | EHR → openPractice | A held date-of-birth change was rejected. Lists the held versions. |
| `demographics.correction_requested` | EHR → openPractice | A proposed change to legal name or date of birth, from EHR staff or intake (ruling 17) |
| `correction.ack` | openPractice → EHR | `received` or `rejected` |
| `correction.resolved` | openPractice → EHR | `applied` (with the new version) or `declined` (coded reason) |
| `link.review_resolved` | raising side → other | `confirmed` or `revoked`. Only the raiser sends it, and `confirmed` is forbidden for case 2. |
| `link.revoked` | either side | Revocation always wins |
| `message.ack` | either side | Acknowledges review resolutions, revocations, hold rejections and correction resolutions |

**Single resolver** (EB-05). The side that raised a review resolves it. Revocation wins over any confirmation. Case 2 (two active `openpractice` links on one EHR patient) can never resolve as `confirmed`.

**Date-of-birth hold** (EB-08 and openChart PL-08). The hold is a link-level suspension. The EHR compares each incoming snapshot's date of birth with its currently applied value, so a later full snapshot cannot bypass the hold. While a hold is open, every later version is held too, and openPractice suspends delivery on that link.
- **Confirm:** the EHR applies the latest held snapshot.
- **Reject:** the EHR sends `demographics.hold_rejected`. Delivery stays suspended until openPractice staff correct the date of birth or re-assert it. This cannot deadlock, because confirming applies the latest held snapshot and rejecting needs a human act on one side to resume.

**Deterministic "agree"** (openChart PL-05). The structured legal name is projected to a single string: given names, then family name, then suffix, joined by single spaces. That string and `OC Patient.patient_name` are each normalized (Unicode NFKC, case-folded, trimmed, internal whitespace collapsed) and compared. Dates of birth must be equal. An empty `OC Patient.date_of_birth` sends the request to identity review.

**No PHI in diagnostics** (EB-12). Reasons are always codes from the schema.

### 5. The clinical-facts contract: `openpractice-ehr-clinical-facts/1.0`

The canonical schema is `contracts/openpractice-ehr-clinical-facts-1.0.schema.json`, SHA-256 `6b2071fe61693d144aa91f4fc68e51d0cd7563d66b5cd2086f281a959f7e69ec`. The contract has three parts:
- pointer-only notifications;
- a `capability.result` stating whether the EHR supports billable facts;
- `facts.result` reads limited to the billing subset.

Codings carry a system and a code only, never descriptor text (FD-11). openChart has no clinician encounters yet. Until it does, it reports `billable_facts_supported: false`, and linked patients' charges are entered manually (EB-17, ruling 21).

**Deadline escape** (EB-18). If a link in identity review would stall billing past the filing threshold, the claim is released from that gate. Staff are notified, the charge is entered manually, and the claim proceeds at tier 3. The link is never auto-confirmed.

### 6. The billing–accounting boundary and banks

openPractice owns billing and the receivables. The accounting system owns the ledger, bank balances and the full bank reconciliation (BAB-01, BAB-02). FD-3 holds, generalized to every destination: immutable postings, dated reversals, frozen batches, and no openPractice period close (BAB-04, BAB-05). This change does not specify the daily balanced day and claims nothing about it.

Banks (ruling 5) are covered by these requirements:
- BAB-09: a register of every practice account.
- BAB-10: full numbers encrypted and role-restricted (ruling 23).
- BAB-11: payer deposit links per payer × billing entity (TIN + NPI).
- BAB-12: the expected-account check.
- BAB-13: a re-enrollment worklist when bank accounts change.
- BAB-14: openPractice reconciles deposits only.
- AI-17: revenue lines are read from the destination, with statement upload as fallback.

**Reconciling 5(e) with the enrollment brainstorms.** The provider-enrollment brainstorm keys enrollment cases by "participation purpose". Deposit enrollment is modeled as a separate purpose with the same case lifecycle and evidence. The enrollment-gated-billing brainstorm makes participation the only claim gate. Deposit enrollment therefore never holds a claim. An active deposit link sets the expected payment method and expected account instead. This is an interpretation of ruling 5(e) and is recorded as such.

### 7. The accounting contract: `openpractice-accounting-integration/1.0`

The canonical schema is `contracts/openpractice-accounting-integration-1.0.schema.json`, SHA-256 `04125b91f66abcd5ae4df62ba0413cb1f524be2227b6f49326a86bc5161f758c`.

**Operations** (AI-01): `customer.upsert`, `invoice.post`, `debit_note.post`, `credit_note.post`, `payment.post`, `payment.apply`, `reversal.post`, `refund.post`, `deposit.post`, `journal.post`, `batch.export`, `bank_lines.read` and `reconciliation.report`. Versions are compatible when they share a MAJOR.

**Keys** (AI-05). Each posting gets a `posting_id` minted at assembly, and its key is `<destination_id>:<posting_id>`. A replacement gets a new `posting_id` with a `supersedes` link. Summary lines use `<destination_id>:<batch_seq>:<line_no>`. Keys never carry personal or account data.

**Batches** (AI-06). A person releases each batch explicitly. Release freezes the batch and records a digest computed over its RFC 8785 canonical JSON. Delivery state, including sync timestamps, is stored apart from batch content and is never an accounting date (AI-07).

**Mode** (AI-09, ruling 7). Mode is chosen before a destination's first released batch. After that, changes are refused until the owner ratifies a continuity procedure.

**ERPNext** (AI-04, ruling 8). The adapter uses the REST API only, whether ERPNext is same-site or remote. The remote topology satisfies acceptance.

**Scope of customers** (AI-10, AI-22, ruling 24). This contract creates and posts to two kinds of customer only, both from medical billing:
- guarantor accounts, including self-pay and direct healthcare charges (FD-4);
- payers.

Ruling 3's no-name rule governs those customers. It is not a ban on named commerce customers that other governance may define in the same destination. This contract never copies care linkage or PHI into such a record, and never posts membership, subscription or retail obligations. Each obligation is therefore posted by exactly one route.

### 8. The posting map (patient-level mode)

| Event | Posting |
|---|---|
| Claim-billed charges finalized | `invoice.post` to the payer customer. GL date = date of service (or first open date + late flag); invoice date = finalization date; due date from terms; date of service on each line. |
| Expected contractual adjustment (payer with learned baseline) | `credit_note.post` at the date of service, label `expected_contractual_adjustment` (AI-12) |
| Remittance true-up | `credit_note.post` or `debit_note.post` for actual minus expected, label `contractual_adjustment_true_up` |
| Payer without history | Gross at the date of service. `credit_note.post` for the full `contractual_adjustment` at remittance. |
| Patient responsibility | `credit_note.post` (transfer) on the payer invoice, plus `invoice.post` to the guarantor account |
| Self-pay charges finalized | `invoice.post` to the guarantor account |
| Collect-at-visit payment before the invoice | `payment.post` as an unapplied payment (customer credit), later `payment.apply` |
| Payment received | `payment.post` into clearing, applied to the invoice |
| Customer credit | Held unapplied until `payment.apply` or `refund.post` |
| Chargeback / returned item | `reversal.post` of the payment, plus `journal.post` of the chargeback or bank fee |
| Payer debit / takeback | `reversal.post` (whole or part) of the recovered payment allocation |
| Provider-level offsets in a wire | Named `journal.post` lines in the deposit's composition: interest, forwarding balance, takeback |
| Virtual-card payment | Net of a `journal.post` virtual-card fee |
| Merchant or bank fee | `journal.post` to its mapped expense account |
| Unidentified receipt | `journal.post` to an unidentified-receipts liability. When identified: release the liability and `payment.post` to the customer, linked by `supersedes`. |
| Legacy share of a deposit | `journal.post` to the mapped legacy-collections account (BAB-08) |
| Explained deposit | One `deposit.post` equal to the bank line, made of clearing releases and named lines |
| Write-off | `credit_note.post` with label `write_off` |
| Correction | `reversal.post` with a back-reference |

In summary mode, one journal group is posted per explained bank line, or per day. It is split by reason category with neutral labels and carries no individual data.

**Neutral labels.** Every line carries a fixed neutral label (AI-16) that never reveals the type of service. A practice's internal reason category stays inside openPractice.

### 9. Posting modes and the BAA gate

Every destination starts in `summary`. Patient-level mode requires one of two records (AI-14, ruling 6):
- a verified BAA with the vendor;
- for a practice-operated destination, a verified attestation naming the operator, which is the practice itself or an existing business associate whose BAA covers the accounting site.

Enabling patient-level mode is tier 4 (ruling 12). If the BAA lapses, delivery halts and nothing is converted (ruling 7). No vendor's BAA posture is asserted.

### 10. Adapter notes

**ERPNext (MVP).** The adapter uses the REST API only (ruling 8). It is expected to map to Customer, Sales Invoice (including returns), Payment Entry and Journal Entry, verified against the ERPNext version pinned in planning. Where the posting key is carried on ERPNext documents is decided in planning, with no direct table writes. Packaging is decided in planning, with the constraint that openPractice core never imports the adapter. Go-live requires AI-20 verification.

**QuickBooks Online and Microsoft Dynamics 365 Business Central (post-MVP, contract level only).** Each must implement the same operations and tests. Before planning, the following must be verified with the vendor: authentication, API and object model, rate limits, idempotency, data residency, line service dates, cash-basis reporting, and BAA availability. This change asserts none of them.

QuickBooks Online is a vendor-operated cloud service. Business Central is offered as a vendor cloud service and is also licensed for on-premises deployment. Either way it is a proprietary single-vendor product. Under Principle VI, both are optional adapters, and core never depends on them.

## Governance tiers (FD-5)

| Action | Tier |
|---|---|
| Create patient | 1 |
| Edit demographics, contacts, confidential-communication instructions, coverage or guarantor; deactivate or reactivate | 2 |
| Hard delete of a record created in error | **4** (T-13) |
| EHR link `create_new` or `link_existing`; identity-review resolution; link revocation | 2 (the explicit human confirmation is the approval) |
| Accept or decline an EHR correction request; re-assert a rejected date of birth | 2 |
| Practice bank register or payer deposit-link change | 2 |
| Change an account mapping | 2 |
| Release an export batch | Explicit human action (tier 2). No tier-3 countdown while R-2 is unresolved. |
| Enable patient-level mode (before first release only) | **4** (T-13) |
| Change mode after first release | Refused until a continuity procedure is ratified |

**Deadline escapes (FD-5).** Every gate names its escape:
- A link in identity review that would stall a claim past the filing threshold releases the claim to manual entry, then a tier-3 claim batch, with notification (EB-18).
- The two tier-4 gates guard no deadline-bearing work, so nothing expires while they wait. Their escape is escalation, not release.
- The date-of-birth hold, the BAA gate and batch release stall no claim, because claims use openPractice demographics and accounting postings carry no filing deadline.

## Constitution Check (pre-handoff, against v1.0.0)

| Principle or gate | Obligation | Status |
|---|---|---|
| I. HIPAA by design | Every change and every PHI read is audited, tamper-evidently (PR-10). No PHI in logs, errors or diagnostics (EB-12, AI-19). Bank numbers are encrypted and restricted (BAB-10). Encryption in transit and at rest. Roster-derived minimum-necessary access. | Requirement level; a safeguard plan is owed per feature |
| II. Security review | Every feature touches PHI, money or credentials. Each needs a review and a risk-analysis update, and each destination needs a risk entry before patient-level mode. Batch release is explicit; nothing autonomous is on by default. | Owed per feature; R-2 avoided, not resolved |
| III. Test-first | Contract tests at the EHR and accounting boundaries against the canonical schemas. Calculation tests for accrual valuation and true-up. Synthetic fixtures only. | Owed per feature |
| IV. Clinical boundary and originality | References and claim-as-submitted copies only. Manual charges are marked manual. Adapters are original code using documented REST interfaces. | Satisfied |
| V. Versioned contracts | Three contracts, each with one canonical schema and digest. REST only to ERPNext in the MVP. No database access in either direction. | Satisfied |
| VI. Portability | ERPNext is self-hostable. Proprietary adapters are optional. | Satisfied |
| Data gate | Synthetic fixtures. No credentials or bank numbers in commits. | Requirement level |

## Adversarial review dispositions

| Finding | Disposition |
|---|---|
| B1 mode switching double-counts | AI-09 locks mode at first release (ruling 7) |
| B2 date-of-birth hold bypass and deadlock | Link-level suspension; `demographics.hold_rejected`; publication suspended (EB-08, PL-08) |
| B3 design calls frozen as requirements | Rulings recorded; text aligned; every remaining open question routed in `tasks.md` §3 |
| M1 tier 4 beyond FD-5 | Ruling 12; T-13; deadline escapes above |
| M2 Feature 4 consumes the Thread | `tasks.md` §2.4 is gated on the FD-7 Thread contract |
| M3 posting map incomplete; legacy versus deposit | AI-13; Decision 8; BAB-08 legacy share |
| M4 idempotency key unsound | AI-05 posting IDs, `supersedes`, summary keys, no personal data |
| M5 EHR issuer undefined; contract identity by name only | EB-02, EB-03, PL-03, PL-04; canonical schemas plus digests; task 1.3 compares digests |
| M6 two resolvers | EB-05 and PL-06 single resolver; revoke wins; case 2 never `confirmed`; `link_id` counters |
| M7 "agree" against the DocType | Deterministic projection and normalization; empty date of birth goes to review (PL-05) |
| M8 hard delete versus retention | Ruling 9; PR-12, PR-13; dependency note |
| M9 deactivation blocks past services | PR-11 refuses only activity dated after deactivation |
| M10 no-PHI diagnostics; guarantor name; service-revealing labels | EB-12, AI-19, PL-13; AI-10, AI-16; neutral-label enumeration in the schema |
| M11 same-site acceptance | Ruling 8; AI-04; `tasks.md` §2.3 |
| m1 coverage count | Zero or more (PR-07, `proposal.md`) |
| m2 mode continuity | AI-09 |
| m3 compatibility rule | Same MAJOR (EB-10, AI-01, PL-12) |
| m4 daily balanced day | No longer claimed (Decision 6, `proposal.md`) |
| m5 acknowledgements; `link.revoked` | EB-07 acknowledgement set; PL-11 |
| m6 free-text reasons | Coded reasons only (EB-06, schema) |
| m7 audit editability | Editable by no one, tamper-evident (PR-10); T-14 |
| m8 brainstorm constructs in binding text | Defined inline (BAB-14, AI-17, AI-18, EB-14) |
| m9 Business Central characterization | Corrected (Decision 10) |
| m10 durable notifications; manual entry for linked patients | openChart PL-14; EB-17 |

## Conflicts and tensions

| # | Tension | Status |
|---|---|---|
| T-1 | The protocol's one-feature handoff | **Ruled A** (ruling 1); owner to confirm in `owner-ratification.md` |
| T-2 | FD-7: Thread contract before any consumer | Features 3 and 4 are gated on it |
| T-3 | FD-3 wording ("QuickBooks") | Generalized to every destination |
| T-4 | Money-lines defaults (deposit-level, cash basis, file-only bank reads, "not ours to explain") | Superseded: patient-level invoices with accrual valuation (ruling 22); the bank is read from the destination (ruling 5); "accounting system's" labels |
| T-5 | Two systems reconciling the bank | **Resolved** by ruling 5: openPractice reconciles deposits only |
| T-6 | R-2: tier 3 versus Principle II | Avoided by explicit release; not resolved |
| T-7 | Retention change versus hard delete | **Resolved** by ruling 9; dependency note in Decision 3 |
| T-8 | Principle V and in-process ERPNext calls | **Resolved for the MVP** by ruling 8 (REST only). In-process use stays conditional on counsel (Open Question 2). |
| T-9 | BAA wording versus a practice-operated destination | **Resolved** by ruling 6 |
| T-10 | openChart cannot yet supply billable facts | Manual entry (ruling 21); the encounter capability stays open in openChart |
| T-11 | Interoperability brainstorm direction | Reversed for administrative data |
| T-12 | "First staged proposal" recommendation | This change departs from it, as recorded in `proposal.md` |
| T-13 | **Tier 4 beyond FD-5's list.** FD-5 reserves tier 4 for refunds, material adjustments and merges. This change adds hard delete (an irreversible identity change) and patient-level enablement (an irreversible PHI disclosure). | **Conscious exception** (ruling 12) |
| T-14 | **Tamper-evident audit versus removal of audit values** (PR-10 versus PR-13) | Mechanism chosen in planning (Decision 3) |

## Risks / Trade-offs

- **[PHI in a third party's books]** → Mitigated by:
  - account-number-only customers and neutral labels;
  - the BAA gate or operator attestation, enabled at tier 4;
  - a delivery halt on lapse;
  - a risk entry per destination.
- **[Duplicate or lost postings]** → Posting IDs, `supersedes` links, lookup before retry, and delivery state kept apart from batch content.
- **[Bookkeeper edits]** → Reconciliation exceptions; edits are never imported.
- **[Wrong-patient link]** → Explicit confirmation, a deterministic "agree" check, the date-of-birth suspension, and single-resolver reviews that never merge.
- **[EHR outage]** → The outbox queues messages. Visits stay visible as waiting on clinical documentation. Manual entry and the deadline escape protect the filing window.
- **[Contract drift between repositories]** → One canonical schema per contract, compared by digest in both changes' task 1.3.
- **[Mode lock-in]** → Mode is deliberately locked after first release. Changing it needs a ratified continuity procedure; there is no fallback mitigation.
- **[Vendor change]** → Adapters sit behind one contract, and each is verified before go-live (AI-20).
- **[Bank-number exposure]** → Encryption, role restriction, audited views, and exclusion from every outbound path.

## Migration Plan

1. Validate strictly. The owner completes `owner-ratification.md` and confirms handoff shape A.
2. Feature 1 (patient registry, Slice 0).
3. Feature 2 (EHR patient link, Slice 0), tested against an openChart double.
4. Separately governed work: the reason taxonomy and money ledger, and the FD-7 Thread contract.
5. Feature 3 (accounting contract, banks and the ERPNext adapter, Slice 4), after step 4. The remote ERPNext topology over REST is used for acceptance. Patient-level mode is set before the first release, only after the BAA gate or attestation.
6. Feature 4 (clinical-fact consumption, Slice 3), after the Thread contract. Manual entry applies while the EHR reports no billable-facts capability.
7. Post-MVP: QuickBooks Online and Business Central adapters, after vendor verification.
8. Rollback disables a destination or link and holds undelivered work. It never deletes postings, audit records or links.
9. Archive only after the features land, the root pin bump lands, and `closure-evidence.md` is complete.

## Open Questions

Every remaining question is routed in `tasks.md` §3.5 to the feature or gate it blocks.

1. **Appointment authority (R-7).** Blocks any appointment exchange. Outside the 1.0 contracts.
2. **Counsel on same-site in-process ERPNext calls.** Covers GPL-3.0 ERPNext and Principle V. Blocks only a future in-process adapter change; not the MVP.
3. **Frappe version policy** for co-installing ERPNext on the same bench. Blocks same-site deployment support in Feature 3.
4. **Legacy collections account.** The bookkeeper's call. The legacy share posts to a mapped account (BAB-08). Blocks Feature 3's first release for a practice with legacy run-off.
5. **Mode continuity procedure** (opening-AR true-up). Blocks any mode change after first release (AI-09). Not part of the MVP.
6. **R-2** (tier 3 versus Principle II). Avoided here, because release is explicit. Blocks any future automatic batch release.
7. **Two receivable routes in one destination** (ruling 24). How do medical-billing receivables and membership or retail receivables coexist in one destination? This covers customer namespace collisions, a person appearing both as an account-number customer and as a named commerce customer, and reconciliation across the two routes. Tracked with the `openpractice-customer-financial-separation` brainstorm packet (in progress). Blocks Feature 3's go-live only in a destination that also carries membership or retail customers.
