## Context

openPractice is an assembly root whose code leg is empty. The Frappe application `open_practice` does not exist yet. Constitution v1.0.0 (`.specify/memory/constitution.md` in `opensoft/openPractice`, ratified 2026-08-24) is in force. Its principles are:

- I: HIPAA by design
- II: security review for PHI, money, authorization and credentials
- III: test-first development, with contract tests at every integration boundary
- IV: clinical boundary and original implementation
- V: versioned integration contracts only
- VI: deployment portability

The product owner, Brett Heap, made six boundary decisions on 2026-11-14. Later the same day he ruled on 33 follow-up questions (ruling 25 supersedes ruling 3, and ruling 30 supersedes ruling 22); see "Owner rulings (2026-11-14)" below. **Rulings are not ratification.** This change stays `Status: draft` and `owner-ratification.md` stays PENDING until the owner completes it.

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
- Change `define-customer-financial-data-boundaries` (`openspec/changes/define-customer-financial-data-boundaries/`) and its brainstorm packet `openpractice-customer-financial-separation` (`ideation/brainstorm/openpractice-customer-financial-separation-overview.md`, with its `-identity`, `-financial-routing`, `-business-views` and `-synthesis-customer-to-books` documents). Both are merged on `main`, and the change is a draft. It owns customer and party roles, routing by offering, obligation ownership and purpose-bound business views, and it references this change's accounting contract for posting (ruling 26).
- Brainstorm `ideation/brainstorm/openpractice-domain-framework.md` (merged on `main`), proposal status. It covers bounded contexts, the source-of-truth table, receivable options A, B and C, the posting contract and revenue recognition (§5A). Rulings 28 to 31 adopt or amend parts of it. Its posting contract (§5.4, §7) is governed by this change's `openpractice-accounting-integration/1.0` (ruling 26).

## Owner rulings (2026-11-14)

Rows 1 to 33 have the same source: **Brett Heap, 2026-11-14, authoring session.** Rows marked (2026-11-15) have the source **Brett Heap, 2026-11-15, accepting the recommendations on decisions 79 to 98 except 80**, except rows 103 and 107, whose source is **Brett Heap, 2026-11-15, accepting the recommendations on decisions 99 to 111** ("accept your recs for 99-111"); each quotes the question and the accepted wording. The requirement text matches each ruling.

| # | Ruling | Governs |
|---|---|---|
| 1 | Handoff shape **A**: an ordered multi-feature exception. Pre-stated as the owner's ruling; the field in `owner-ratification.md` is left for him to confirm. | `tasks.md` §1.2, §2 |
| 2 | Accounting customers are the guarantor account (patient balances) and each payer (insurance balances). *Refined by ruling 28: this applies only in `customer-detail` mode.* | AI-10 |
| 3 | **Superseded by ruling 25.** Kept as history: a patient-balance customer is identified by account number only. No guarantor or patient name ever reaches the destination, on customers or invoice lines. With guarantor = self, a guarantor-named customer would be the patient's name. | None now; formerly AI-10, AI-16, PR-06 |
| 4 | Dates: line date of service; GL date = date of service if that period is open, otherwise the first open date with a late-charge flag (FD-3); invoice date = finalization date; due date from statement terms; sync timestamp kept separately and never an accounting date; each adapter verifies line service dates and cash-basis support before go-live. *Scope stated in fix round 3 (re-review N11): the GL-date rule governs charge postings; payments, refunds and deposits use their own event or bank date under the same closed-period rule.* | AI-11, AI-07, AI-20, BAB-15 |
| 5 | Banks: a register of all practice accounts; payer deposit links per payer × billing entity (TIN + NPI) with method, account, enrollment status and evidence; an expected-account check; a re-enrollment worklist; these feed the enrollment lifecycle. openPractice reconciles deposits only, reading revenue bank lines from the destination with statement upload as fallback. Other lines are labelled "accounting system's". One deposit entry per explained deposit. The accounting system owns bank balances and the full reconciliation. | BAB-02, BAB-09, BAB-11, BAB-12, BAB-13, BAB-14, AI-17 |
| 6 | A practice-operated destination (for example self-hosted ERPNext) satisfies the BAA gate with an attestation naming the operator. | AI-14 |
| 7 | Posting mode is chosen before a destination's first released batch. Every destination starts in `summary-journal` and may move to `customer-detail` only before the first release, after the BAA gate. After that, any mode change is refused until the owner ratifies a continuity procedure. Mapping is versioned per destination and mode. The "fall back to summary" mitigation is dropped. A BAA lapse halts delivery with no silent conversion. *Refined by ruling 28: `summary-journal` is the default and recommended setup, and `customer-detail` is a per-destination opt-in.* | AI-09, BAB-06, AI-14 |
| 8 | The MVP calls ERPNext only through its REST API, same-site or remote, with no in-process calls. In-process use stays conditional on counsel. The remote topology alone satisfies MVP acceptance. | AI-04, `tasks.md` §2.3 |
| 9 | A record created in error with no linked activity is outside every retention record family. Hard delete is tier 4 and removes the live content and the PHI values in that record's audit trail, leaving a PHI-free tombstone. Backups age out under backup retention. No unscoped "permanent" claim. `add-reimbursement-retention-control` must carry this exclusion when ratified. | PR-12, PR-13, PR-10 |
| 10 | Confidential-communication instructions are in the MVP registry and carried in the link snapshot. | PR-05, EB-06 |
| 11 | Preferred language is added. Preferred name stays. Sex assigned at birth and sexual orientation stay out (clinical; owned by openChart). | PR-03, EB-06 |
| 12 | Tier 4 applies only to hard delete and to enabling `customer-detail` mode, as a conscious FD-5 exception (T-13). `link_existing` and review resolution are tier 2. The FD-5 deadline escape is stated for gates. | PR-12, AI-14, EB-04, EB-05, EB-18 |
| 13 | Coverage is not in link contract 1.0; it may come in a later MINOR. | EB-06 |
| 14 | The date-of-birth hold is accepted, implemented as a link-level suspension. | EB-05, EB-08 (openChart PL-08) |
| 15 | The "agree" check for `link_existing` is accepted and made deterministic against the built DocType. | EB-05 (openChart PL-05) |
| 16 | The applied legal name and date of birth are projected onto `OC Patient`, with history. `sex_recorded` is never overwritten. | openChart PL-09 |
| 17 | openChart refuses direct edits to a linked patient's projected fields and routes them to openPractice as correction requests; intake flows included. | EB-09 (openChart PL-10) |
| 18 | The openChart integration role is named and scoped in planning, narrower than System Manager. | openChart PL-05 |
| 19 | Namespace tokens `openpractice` and `openchart` are confirmed. | EB-01 |
| 20 | Coverages are zero or more; self-pay patients have none. | PR-07, `proposal.md` |
| 21 | Manual charge entry is allowed for a linked patient while that EHR reports no billable-facts capability. It is marked manual, with provenance. | EB-17 |
| 22 | **Superseded by ruling 30.** Kept as history: accrual valuation posts the billed charge plus an expected contractual adjustment at the date of service, from the payer's observed history (FD-2), and trues up at remittance. Fall back to gross at date of service, with the adjustment at remittance, when no history exists. | None now; formerly AI-12 |
| 23 | Full bank account and routing numbers are stored encrypted, visible only to owner and billing-admin roles, masked elsewhere, every view audited, and never in logs, errors, exports, postings or keys. | BAB-10, AI-16, AI-19 |
| 24 | Membership agreements, subscription renewals, retail sales and other non-medical commerce invoicing are out of scope. Their routing and ownership are being worked out in the `openpractice-customer-financial-separation` brainstorm packet (in progress; to be linked when it lands). Ruling 3 is scoped to customers created by this contract from medical billing: guarantor accounts (including self-pay and direct healthcare charges under FD-4) and payers. It does not govern membership or retail customer records. The contract never copies care linkage or PHI into a customer record it does not own, and never posts membership or retail obligations. How the two kinds of receivable coexist in one destination is an open question. *Revised by ruling 26: routing and obligation ownership now belong to change `define-customer-financial-data-boundaries`, and "never posts membership or retail obligations" now reads "never creates a second collectible balance for them". Ruling 3 itself is superseded by ruling 25.* | AI-10, AI-16, AI-22, `proposal.md` non-goals, Open Question 7 |
| 25 | **Supersedes ruling 3.** A medical-billing customer (a guarantor account, including direct-pay healthcare and self-pay; payers were already named) may carry its name and billing address or contact in the destination once that destination's BAA gate is satisfied, and only in `customer-detail` mode. `summary-journal` mode carries no customer records or names. The minimum necessary is name plus billing address or contact. Always excluded: chart notes, diagnoses, medications, results, full claim payloads, EHR medical-record numbers and service-revealing reason categories. Any additional field needs a documented purpose, recipient and authority, through a versioned recipient profile. `define-customer-financial-data-boundaries` states the same rule in customer-role-boundaries, "Accounting receives a limited customer and financial record": the same exclusions, service-revealing reason categories included, and the BAA gate for every PHI-bearing customer record, enforced by its routing against this change's destination register (AI-14). The no-PHI rules for logs, errors, keys and diagnostic bundles stay. *Refined by ruling 28: this applies only in `customer-detail` mode.* | AI-10, AI-15, AI-16, AI-19, AI-21, AI-23; schema `customer`, `recipientProfile`, `recipientProfileDocument` |
| 26 | **Ownership split.** This change owns the posting mechanics of `openpractice-accounting-integration/1.0`: posting identity and idempotency, batches and their freeze, release, retries, the reconciliation report, the posting modes and the BAA gate. Change `define-customer-financial-data-boundaries` owns customer and party roles, routing by offering (insurance-billed healthcare, direct-pay healthcare, membership, retail), obligation ownership and purpose-bound business views, and references this contract for posting. Membership and retail obligations are owned by that change's routes, and this contract never creates a second collectible balance for them. | AI-01, AI-22, AI-23, Decision 7, ownership matrix, T-15, T-16, Open Question 7, `proposal.md` |
| 27 | Confidential-communication instructions stay in the link snapshot. openChart, and anything it drives (for example HealthLinc), must honor them for every patient communication it originates. This settles re-review finding N6. | EB-06; openChart PL-16 |
| 28 | **Accounting posture; refines rulings 2, 7 and 25.** The MVP default and recommended setup is `summary-journal`, the framework document's Option A: summarized, balanced postings to payer-class control accounts, with patient-level receivable detail kept in openPractice. `customer-detail` stays in the contract as a per-destination opt-in, enabled only after that destination's BAA gate and only before its first release (ruling 7). Rulings 2 and 25 apply only in `customer-detail` mode. Reconciliation: openPractice's subledger total equals the destination's control account, per payer class. | AI-08, AI-09, AI-10, AI-15, Decisions 7 to 9, `proposal.md` |
| 29 | **Demographics.** Boundary decision 5 stands: openPractice is the source for administrative demographics and publishes them to a linked EHR, and each system owns its own record. | EB-01, EB-06 (unchanged) |
| 30 | **Revenue recognition; supersedes ruling 22.** The framework document's D5 and D8 (§5A) are adopted. Net revenue is the constrained expected entitlement, estimated at the date of service and re-estimated at each close; the gross charge is a memo or gross line carried with its deduction. Billing, adjudication, cash and disputes are evidence events that update the estimate, not revenue triggers. A payer with no history gets a conservative constrained estimate, with no gross-then-adjust fallback. Re-estimates and true-ups are new dated entries, never edits (FD-3). Ruling 4's dates still hold for charge lines, and a close re-estimate is dated in the period being closed if it is open, otherwise on the first open date. D8: the practice's accountant approves the reporting basis, estimator, classification, materiality and reopening policy before go-live. | AI-11, AI-12, AI-15, AI-20, AI-21, schema `estimateRef`, `tasks.md` §2.3, `closure-evidence.md` |
| 31 | **Appointment authority; closes R-7.** openPractice is the system of record for the administrative appointment; the EHR owns the encounter and keeps an appointment reference if needed. The 1.0 contracts carry no appointment operations; appointment events are a future MINOR version. | EB-19, ownership matrix, Open Question 1 |
| 32 | **Framework D1.** One Party with roles. "Customer" is a party role only for membership and retail buyers. For medical billing, the accounting-side customer (a guarantor account or payer, in `customer-detail` mode only) is derived from the guarantor or payer role and is not a separate party role. | AI-10, Decision 7; `define-customer-financial-data-boundaries` customer-role-boundaries |
| 33 | **Framework D7; FD-5 governs.** Automation stays propose-only. Each action's FD-5 tier decides whether it needs an approval, a notification or a delay-and-cancel release, including the deadline escapes. Where a tier calls for an approval, release decision or attestation, the signer may be a named human or a named AI agent identified by an openWallet ID (opensoft/openWallet; identity mechanics decided in planning). Agent signing is opt-in per action type and never on by default. Every sign-off records the signer's identity, including an agent's openWallet ID, with provenance. Tier 4 stays human-only: hard delete, enabling `customer-detail`, refunds leaving the practice, material adjustments and record merges. | BAB-17, AI-06, EB-17, governance tiers, T-17 |
| 87 | (2026-11-15) Question: which diagnoses does openChart publish to openPractice as billable facts? Accepted: "Only confirmed ones. If a published diagnosis is later refuted, a retraction is sent." | EB-13 (confirmed diagnoses only), EB-15 (the retraction carries `withdrawn`), clinical-facts schema `condition` |
| 88 | (2026-11-15) Question: a patient link is revoked or superseded while corrections are queued; do the retractions still go out? Accepted: "Yes. Retractions of facts already published under that link are delivered, as pointers only. No new facts and no reads are served. Otherwise openPractice keeps billing withdrawn facts." | EB-15 (accepted after a link ends), EB-13, clinical-facts schema `link_id` note |
| 89 | (2026-11-15) Question: the clinical-facts contract has only one retraction message, "entered in error", now also used where that reason is wrong. Accepted: "Add a retraction reason code to the contract now, while 1.0 is still unratified. That is a small follow-up PR in openPractice-spec …" The same follow-up also carries the erratum to the gateway change's companion list. | EB-15, clinical-facts schema `retraction_reason`, F-1 to F-3 |
| 93 | (2026-11-15) Question: follow-up change to the merged openPractice contracts. Accepted: "Yes, one draft PR carrying: the agent operations Hermes lacks and the consent check; a verifiable entitlement; a clinician-acknowledged status; the representative's relationship and authority; the retraction reason code …" The list ends with an erratum to the gateway change's companion list: agent operations belong to medXfactory change `define-care-organization-hermes-openpractice-operations`, and clinical AI answering to medXfactory change `define-patient-medical-ai-connection`. | Here, the retraction reason code only; "Follow-up of 2026-11-15" below |
| 103 | (2026-11-15) Question: one money format for every contract. Accepted: "Decimal text plus a currency code on the wire, as the accounting contract already does. Whole cents in storage." | BAB-18; AI-02; accounting schema `money`, `currency` and the bank-lines result's `currency`; F-8 to F-10 are design calls under it |
| 107 | (2026-11-15) Question: one person in several roles. Accepted: "Allowed, with each sign-off recording the role used. A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change." | PR-15 (a staff member who is also a patient, guarantor, subscriber or representative); F-7 is a design call under it. The readiness checklist and starter policies belong to a new change, not this one |

**Mode names.** To implement ruling 26, the posting-mode names were aligned across this change, `define-customer-financial-data-boundaries` and openChart's `define-openpractice-boundary`. The pair is `summary-journal` and `customer-detail`; they replace this change's earlier "summary" and "patient-level". Rows 7 and 12 use the new names, and their substance is unchanged.

## Goals / Non-Goals

**Goals**

- Specify the administrative patient record precisely enough that the first feature can be built test-first.
- Make the EHR boundary bidirectional and explicit: links, issuers, single-resolver identity review, demographic publication, correction requests and clinical facts. Together with ruling 31 on appointments, this closes R-7.
- Fix the split between billing and accounting so that openPractice never becomes a ledger.
- Specify one accounting contract for three destinations, and build the ERPNext adapter in the MVP.
- Gate PHI in the accounting system per destination, and minimize what is sent.

**Non-Goals**

- Duplicate scoring, a merge queue, card OCR and household modeling.
- Appointment operations in the 1.0 contracts; appointment events come as a later MINOR version (ruling 31).
- QuickBooks Online and Business Central adapters.
- Authoring the Thread contract or the reason taxonomy.
- Routing and owning membership agreements, subscription renewals, retail sales and other non-medical commerce obligations. They belong to the routes of change `define-customer-financial-data-boundaries` (rulings 24 and 26), and this contract never creates a second collectible balance for them.
- Defining customer and party roles, offering routes or purpose-bound business views (ruling 26).
- Making vendor BAA or API claims.
- Changing another repository's runtime.

## Ownership matrix

| Object or function | openPractice | EHR | Accounting system |
|---|---|---|---|
| Administrative demographics, contacts, confidential-communication instructions, preferred language | **Source** | Mirror (link contract) | — |
| Guarantor, coverage | **Source** | — | Guarantor-account customer: the account number, plus name and billing address or contact (`customer-detail` only, after the BAA gate; ruling 25) |
| Clinical patient anchor; encounters, diagnoses, procedures, documentation | References and claim-as-submitted copies | **Source** | — |
| Notes, medications, results, orders | — | **Source** | — |
| Appointments | **Source** (ruling 31) | Reference if needed; owns the encounter | — |
| Charges, claims, statements, collections, payer and patient receivables, allocation, refunds, financial audit lineage | **Source** | — | Category totals to payer-class control accounts (`summary-journal`, the default) or a mirror (`customer-detail`, opt-in) |
| Reason taxonomy | **Source** | — | Accounts mapped per destination and mode |
| Practice bank account register; payer deposit links | **Source** | — | Mapped bank accounts |
| Revenue bank-line explanation (deposits, chargebacks, takebacks, processor fees, refund disbursements); single poster of each explained deposit | **Owner** | — | Supplies the lines |
| Bank balances and full bank reconciliation | — | — | **Source** |
| General ledger, chart of accounts, taxes, payroll, payables, financial statements | — | — | **Source** |
| Identity linkage | Stores (EHR issuer, namespace, value) | Stores (openPractice issuer, `openpractice`, value) | — |
| Posting mechanics: identity and idempotency, batches and freeze, release, retries, reconciliation report, posting modes, BAA gate | **Owner** (ruling 26), for every route that posts through openPractice | — | Receives postings |
| Customer and party roles; membership, subscription and retail obligations and their customers | **Out of scope here** (rulings 24 and 26): owned by `define-customer-financial-data-boundaries`. This contract never copies care linkage into those records and never creates a second collectible balance for those obligations. | — | Per the owning route in that change |

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

Deactivation (PR-11) refuses only activity dated after the deactivation date. Services already given, including to a patient who has since died, stay chargeable. The wording does not depend on who owns appointments, which ruling 31 has since settled.

Hard delete (PR-12, PR-13) applies only to a record created in error with no linked activity. Such records are outside every retention record family (ruling 9). Hard delete is tier 4 and removes the live content and the PHI values in that record's audit trail, leaving a PHI-free tombstone. Backup copies age out under backup retention, and no unscoped "permanent" claim is made.

**Dependency.** When `add-reimbursement-retention-control` is ratified, it must carry this exclusion. This is a dependency note only; this change does not edit that one.

**New tension T-14.** PR-10 makes audit records tamper-evident and editable by no one, but PR-13 removes PHI values from one record's audit trail. The design that satisfies both keeps audit values outside the tamper-evident chain, which records only a keyed commitment to each value. The sanctioned removal deletes the value and its key, and appends a redaction event. Verification must flag any commitment that has neither its value nor a redaction event (PR-10). The Speckit plan must choose the mechanism.

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

**Single resolver** (EB-05). The side that raised a review resolves it. Revocation wins over any confirmation. Case 2 (two active `openpractice` links on one EHR patient) can never resolve as `confirmed`, and neither can its mirror, which openPractice raises when an EHR reference returned to it is already linked to a different openPractice patient.

**Date-of-birth hold** (EB-08 and openChart PL-08). The hold is a link-level suspension. The EHR compares each incoming snapshot's date of birth with its currently applied value, so a later full snapshot cannot bypass the hold. While a hold is open, every later version is held too, and openPractice suspends delivery on that link.
- **Confirm:** the EHR applies the latest held snapshot.
- **Reject:** the EHR sends `demographics.hold_rejected`. Delivery stays suspended until openPractice staff correct the date of birth or re-assert it. This cannot deadlock, because confirming applies the latest held snapshot and rejecting needs a human act on one side to resume.

**Deterministic "agree"** (openChart PL-05). The structured legal name is projected to a single string: given names, then family name, then suffix, joined by single spaces. That string and `OC Patient.patient_name` are each normalized (Unicode NFKC, case-folded, trimmed, internal whitespace collapsed) and compared. Dates of birth must be equal. An empty `OC Patient.date_of_birth` sends the request to identity review.

**No PHI in diagnostics** (EB-12). Reasons are always codes from the schema.

### 5. The clinical-facts contract: `openpractice-ehr-clinical-facts/1.0`

The canonical schema is `contracts/openpractice-ehr-clinical-facts-1.0.schema.json`, SHA-256 `3aac773e9e8a8a370e1a78c845e5837d059b60146e411d973e0c54464aad12db` (it was `6b2071fe61693d144aa91f4fc68e51d0cd7563d66b5cd2086f281a959f7e69ec` before the follow-up of 2026-11-15). The contract has three parts:
- pointer-only notifications;
- a `capability.result` stating whether the EHR supports billable facts;
- `facts.result` reads limited to the billing subset.

Codings carry a system and a code only, never descriptor text (FD-11). Only `confirmed` encounter diagnoses are published (ruling 87). The single retraction message, `clinical_fact.entered_in_error`, carries a `retraction_reason` (ruling 89): `entered_in_error` for one Condition or Procedure itself entered in error while its encounter stays published, and `withdrawn` for every other retraction, including every encounter-level one, so no code reveals a restricted act (EB-15). A retraction of a fact published under a link that has since ended is still delivered and accepted, as pointers only (ruling 88). openChart has no clinician encounters yet. Until it does, it reports `billable_facts_supported: false`, and linked patients' charges are entered manually (EB-17, ruling 21).

**Deadline escape** (EB-18). If a link in identity review would stall billing past the filing threshold, the claim is released from that gate. Staff are notified. A staff member may then enter the charge manually only after attesting that the patient's identity was verified and that the documentation is final and signed; the attestation is recorded with provenance, and without it the entry is refused (EB-17). The claim proceeds at tier 3. The link is never auto-confirmed.

### 6. The billing–accounting boundary and banks

openPractice owns billing and the receivables. The accounting system owns the ledger, bank balances and the full bank reconciliation (BAB-01, BAB-02). FD-3 holds, generalized to every destination: immutable postings, dated reversals, frozen batches, and no openPractice period close (BAB-04, BAB-05). This change does not specify the daily balanced day and claims nothing about it.

Banks (ruling 5) are covered by these requirements:
- BAB-09: a register of every practice account.
- BAB-10: full numbers encrypted and role-restricted (ruling 23).
- BAB-11: payer deposit links per payer × billing entity (TIN + NPI).
- BAB-12: the expected-account check.
- BAB-13: a re-enrollment worklist when bank accounts change.
- BAB-14: openPractice reconciles deposits only, refund disbursements included, and is the single poster of each deposit it explains (ruling 5). A share that funds another route's balances posts as `other_route_share` to its mapped account.
- BAB-16: charge finalization checks the offering route first, so a membership-included service is never invoiced or claimed as medical.
- AI-17: revenue lines are read from the destination, with statement upload as fallback.

**Reconciling 5(e) with the enrollment brainstorms.** The provider-enrollment brainstorm keys enrollment cases by "participation purpose". Deposit enrollment is modeled as a separate purpose with the same case lifecycle and evidence. The enrollment-gated-billing brainstorm makes participation the only claim gate. Deposit enrollment therefore never holds a claim. An active deposit link sets the expected payment method and expected account instead. This is an interpretation of ruling 5(e) and is recorded as such.

### 7. The accounting contract: `openpractice-accounting-integration/1.0`

The canonical schema is `contracts/openpractice-accounting-integration-1.0.schema.json`, SHA-256 `a244a6df01f93924b456c5cb04ec6abf7870f71d5c9087b8415e89a2cd3af988` (it was `353492ceac5bfa9a90fa7eea221c5727a303453e6232430847224c0807007885` before the corrections of 2026-11-15).

**Ownership** (AI-01, ruling 26). This change owns the contract's posting mechanics: posting identity and idempotency, batches and their freeze, release, retries, the reconciliation report, the posting modes and the BAA gate. Change `define-customer-financial-data-boundaries` owns customer and party roles, routing by offering, obligation ownership and purpose-bound business views. That change references this contract for posting and defines no second posting mechanism.

**Operations** (AI-01): `customer.upsert`, `invoice.post`, `debit_note.post`, `credit_note.post`, `payment.post`, `payment.apply`, `reversal.post`, `refund.post`, `deposit.post`, `journal.post`, `batch.export`, `bank_lines.read` and `reconciliation.report`. Versions are compatible when they share a MAJOR.

**Keys** (AI-05). Each posting gets a `posting_id` minted at assembly, and its key is `<destination_id>:<posting_id>`. A replacement gets a new `posting_id` with a `supersedes` link. Summary-journal lines use `<destination_id>:<batch_seq>:<line_no>`. Keys never carry personal or account data.

**Batches** (AI-06). A person releases each batch explicitly. Release freezes the batch and records a digest computed over its RFC 8785 canonical JSON. Delivery state, including sync timestamps, is stored apart from batch content and is never an accounting date (AI-07).

**Mode** (AI-09, rulings 7 and 28). `summary-journal` is the default and recommended setup. `customer-detail` is a per-destination opt-in, chosen before a destination's first released batch and only after its BAA gate. After that, changes are refused until the owner ratifies a continuity procedure.

**ERPNext** (AI-04, ruling 8). The adapter uses the REST API only, whether ERPNext is same-site or remote. The remote topology satisfies acceptance.

**Scope of customers** (AI-10, AI-22, rulings 24 to 26 and 28). Only in `customer-detail` mode does this contract create and post to customers, and only of two kinds, both from medical billing and each derived from the guarantor or payer role, not a separate party role (ruling 32):
- guarantor accounts, including self-pay and direct-pay healthcare charges (FD-4);
- payers.

Ruling 25 governs those customers. They exist only in `customer-detail` mode, behind the BAA gate. There, a guarantor-account customer may carry the guarantor's name, billing address and billing contact. A further field needs a new recipient-profile version that documents its purpose, recipient and authority, and every customer-detail batch records the profile version it used. The profile shape is defined once, by AI-23 and the schema's `recipientProfileDocument`, and `define-customer-financial-data-boundaries` uses it rather than defining another. `summary-journal` mode creates no customer record. Membership and retail obligations and their customers belong to the routes of `define-customer-financial-data-boundaries`. This contract never copies care linkage or PHI into those records and never creates a second collectible balance for those obligations, so each obligation has one owner and one posting route.

### 8. The posting map

**Revenue recognition** (AI-12, ruling 30). Net revenue is the constrained expected entitlement, estimated at the date of service from a versioned estimator and re-estimated at each close. The gross charge is a gross line carried with its gross-to-net deduction. Billing, adjudication, cash and disputes are evidence events that update the estimate. Each re-estimate or true-up is a new dated entry that references its estimate version or close snapshot. Close re-estimates post automatically from the approved estimator version (FD-3). Only estimator versions and the D8 policy items need approval, by the practice's accountant only, never by an opted-in agent. A payer with no history gets a conservative constrained estimate, and there is no gross-then-adjust fallback. Credit losses are an expense, separate from revenue.

**`summary-journal` (the default, ruling 28).** Balanced category totals post to payer-class control accounts, summarized by period, payer class, benign service category and account (AI-15). Service performed posts the gross line and its deduction. Claim submission reclassifies unbilled to billed receivable. Adjudication realizes the contractual adjustment against the allowance and trues up any difference. Payments move cash clearing against the payer-class or patient receivable, and patient-share transfers move between control accounts. Disputes, credit losses, refund liabilities and reversals are new entries. A day-level group below the destination's minimum group size (default 11 visits) rolls up to the week, then to the period (AI-15). Patient-level and day-level detail stays in openPractice's detail manifest, which the batch identifies by digest. No customer record is created.

**`customer-detail` (opt-in, after the BAA gate).**

| Event | Posting |
|---|---|
| Claim-billed charges finalized | `invoice.post` to the payer customer. GL date = date of service (or first open date + late flag); invoice date = finalization date; due date from terms; date of service on each line. |
| Gross-to-net deduction at the date of service | `credit_note.post`, label `gross_to_net_deduction`, referencing the estimate version (AI-12) |
| Evidence or close re-estimate | `credit_note.post` or `debit_note.post` for the change in estimate, label `gross_to_net_true_up`, referencing the estimate version or close snapshot |
| Payer without history | The same, with a conservative constrained estimate; no gross-then-adjust fallback (ruling 30) |
| Credit loss (authorized) | `journal.post`, label `credit_loss`, an expense separate from revenue |
| Overpayment identified | `journal.post`, label `refund_liability` |
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
| Unidentified receipt | `journal.post` to an unidentified-receipts liability. When identified: release the liability and `payment.post` to the customer, both linked by `applies_to` to the original posting. |
| Legacy share of a deposit | `journal.post` to the mapped legacy-collections account (BAB-08) |
| Share of a deposit funding another route's balances | An `other_route_share` line in the deposit's composition, to its mapped account (BAB-14) |
| Refund disbursement on the bank | Matched to the issued refund's `refund.post` (AI-17) |
| Explained deposit | One `deposit.post` equal to the bank line, made of clearing releases and named lines. openPractice is its single poster. |

Charge postings use the date of service as their GL date. Payments, applications, refunds, deposits and other postings use their own event or bank date, under the same closed-period rule (AI-11, BAB-15).
| Write-off | `credit_note.post` with label `write_off` |
| Correction | `reversal.post` with a back-reference |

**Neutral labels.** Every line carries a fixed neutral label (AI-16) that never reveals the type of service. A practice's internal reason category stays inside openPractice.

### 9. Posting modes and the BAA gate

Every destination starts in `summary-journal`, the default and recommended setup (ruling 28). Opting into `customer-detail` requires one of two records (AI-14, ruling 6):
- a verified BAA with the vendor;
- for a practice-operated destination, a verified attestation naming the operator, which is the practice itself or an existing business associate whose BAA covers the accounting site.

Enabling `customer-detail` mode is tier 4 (ruling 12). If the BAA lapses, delivery halts and nothing is converted (ruling 7). Customer names and billing contacts reach a destination only in `customer-detail` mode with the gate satisfied (ruling 25). No vendor's BAA posture is asserted.

### 10. Adapter notes

**ERPNext (MVP).** The adapter uses the REST API only (ruling 8). It is expected to map to Customer, Sales Invoice (including returns), Payment Entry and Journal Entry, verified against the ERPNext version pinned in planning. Where the posting key is carried on ERPNext documents is decided in planning, with no direct table writes. Packaging is decided in planning, with the constraint that openPractice core never imports the adapter. Go-live requires AI-20 verification.

**QuickBooks Online and Microsoft Dynamics 365 Business Central (post-MVP, contract level only).** Each must implement the same operations and tests. Before planning, the following must be verified with the vendor: authentication, API and object model, rate limits, idempotency, data residency, line service dates, cash-basis reporting, and BAA availability. This change asserts none of them.

QuickBooks Online is a vendor-operated cloud service. Business Central is offered as a vendor cloud service and is also licensed for on-premises deployment. Either way it is a proprietary single-vendor product. Under Principle VI, both are optional adapters, and core never depends on them.

## Governance tiers (FD-5)

| Action | Tier | Signer (ruling 33, BAB-17) |
|---|---|---|
| Create patient | 1 | No sign-off |
| Edit demographics, contacts, confidential-communication instructions, coverage or guarantor; deactivate or reactivate | 2 | No sign-off; notification |
| Hard delete of a record created in error | **4** (T-13) | Named human only |
| EHR link `create_new` or `link_existing`; identity-review resolution; link revocation | 2 (the explicit confirmation is the sign-off; T-17) | Named human, or an opted-in named agent |
| Accept or decline an EHR correction request; re-assert a rejected date of birth | 2 (T-17) | Named human, or an opted-in named agent |
| Manual-entry attestation under identity review (EB-17) | Gate (T-17) | Named human, or an opted-in named agent |
| Practice bank register or payer deposit-link change | 2 | No sign-off; notification |
| Change an account mapping | 2 | No sign-off; notification |
| Release an export batch | Explicit release decision (tier 2). No tier-3 countdown while R-2 is unresolved (T-17). | Named human, or an opted-in named agent |
| Approve an estimator version or a D8 policy item | Approval (D8) | The practice's accountant only; never opted in for agent signing |
| Close re-estimate | Automatic, from the approved estimator (FD-3) | No sign-off |
| Enable `customer-detail` mode (before first release only) | **4** (T-13) | Named human only |
| Refund leaving the practice; material adjustment | **4** (FD-5) | Named human only |
| Record merge (not in the MVP) | **4** (FD-5) | Named human only |
| Change mode after first release | Refused until a continuity procedure is ratified | — |

Agent signing is opt-in per action type and off by default. Every sign-off records the signer's identity, including an agent's openWallet ID, with provenance (BAB-17).

**Deadline escapes (FD-5).** Every gate names its escape:
- A link in identity review that would stall a claim past the filing threshold releases the claim to manual entry after a staff attestation (EB-17), then a tier-3 claim batch, with notification (EB-18).
- A charge held by the offering-route check releases at tier 3 as medical billing at the filing threshold, with notification, a cancel window and provenance (BAB-16, FD-5).
- The two tier-4 gates guard no deadline-bearing work, so nothing expires while they wait. Their escape is escalation, not release.
- The date-of-birth hold, the BAA gate and batch release stall no claim, because claims use openPractice demographics and accounting postings carry no filing deadline.

## Constitution Check (pre-handoff, against v1.0.0)

| Principle or gate | Obligation | Status |
|---|---|---|
| I. HIPAA by design | Every change and every PHI read is audited, tamper-evidently (PR-10). No PHI in logs, errors or diagnostics (EB-12, AI-19). Bank numbers are encrypted and restricted (BAB-10). Encryption in transit and at rest. Roster-derived minimum-necessary access. Accounting customer fields are limited by a versioned recipient profile (AI-10, AI-16). | Requirement level; a safeguard plan is owed per feature |
| II. Security review | Every feature touches PHI, money or credentials. Each needs a review and a risk-analysis update, and each destination needs a risk entry before `customer-detail` mode. Batch release is explicit; nothing autonomous is on by default. | Owed per feature; R-2 avoided, not resolved |
| III. Test-first | Contract tests at the EHR and accounting boundaries against the canonical schemas. Calculation tests for constrained-estimate revenue, evidence true-ups and close re-estimates. Synthetic fixtures only. | Owed per feature |
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
| M4 idempotency key unsound | AI-05 posting IDs, `supersedes`, summary-journal keys, no personal data |
| M5 EHR issuer undefined; contract identity by name only | EB-02, EB-03, PL-03, PL-04; canonical schemas plus digests; task 1.3 compares digests |
| M6 two resolvers | EB-05 and PL-06 single resolver; revoke wins; case 2 never `confirmed`; `link_id` counters |
| M7 "agree" against the DocType | Deterministic projection and normalization; empty date of birth goes to review (PL-05) |
| M8 hard delete versus retention | Ruling 9; PR-12, PR-13; dependency note |
| M9 deactivation blocks past services | PR-11 refuses only activity dated after deactivation |
| M10 no-PHI diagnostics; guarantor name; service-revealing labels | EB-12, AI-19, PL-13; AI-10 and AI-16, where names appear only behind the BAA gate in `customer-detail` mode under a recipient profile (ruling 25); neutral-label enumeration in the schema |
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

## Re-review dispositions (fix round 3)

| Finding | Disposition |
|---|---|
| N1 bank numbers addable by a profile | Moved to the list no profile can add (AI-16). The schema refuses `bank`, `routing` and `acct` field names. |
| N2 and M11 residue | `closure-evidence.md` and PR-06 now state ruling 25 and ruling 8 |
| m2 summary-journal as "fallback only" | AI-10 and `proposal.md`: it is the starting mode and the only mode without a satisfied BAA gate, and a destination stays in the mode its first release used |
| N3 cross-mode deduplication | `define-customer-financial-data-boundaries` now cites AI-09 and single-owner routing |
| N4 mixed settlements | Single deposit poster and `other_route_share` (BAB-14, schema). Offering-route check before finalization (BAB-16). Open Question 7 tightened. |
| N5 deadline escape under identity review | Third EB-17 case with a staff attestation and a refusal path; EB-18 updated |
| N6 confidential communication | Ruling 27; EB-06; openChart PL-16 |
| N7 PHI-bearing membership records | That change: exclusions, label limits, BAA gate enforced by its routing against the AI-14 destination register |
| N8 attestation in ratification statement | `owner-ratification.md` names ruling 6's attestation |
| N9 one recipient-profile shape | AI-23 and `recipientProfileDocument`; that change references it |
| N10 schema enforcement | No payer fields on a guarantor customer; `service_date` required on invoice, debit-note and credit-note lines |
| N11 posting dates | AI-11 and BAB-15 limit the date-of-service rule to charge postings |
| N12 mirror of case 2 | EB-05 bars `confirmed` for it |
| N13 unidentified receipt | AI-13 uses `applies_to` |
| N14 refund disbursements | A reconciled bank-line category (BAB-14, AI-17, schema) |
| N15 governance gate for that change | That change has an `owner-ratification.md` and a strict-validation task |
| T-14 gap | PR-10: verification flags a missing value with no redaction event |

## Verification dispositions (fix round 4)

| Finding | Disposition |
|---|---|
| M-A openChart scope during holds and after revocation | openChart PL-10 and PL-16 |
| M-B close-snapshot approval versus FD-3 | AI-12: close re-estimates post automatically; only estimator versions and D8 items need approval |
| M-C offering-route hold versus FD-5 | BAB-16 releases at tier 3 as medical billing |
| M-D day-level summary groups could identify a visit | AI-15 minimum group size with week and period roll-up; schema `group_level` |
| N7 remainder | That change adds a configuration-time gate |

## Conflicts and tensions

| # | Tension | Status |
|---|---|---|
| T-1 | The protocol's one-feature handoff | **Ruled A** (ruling 1); owner to confirm in `owner-ratification.md` |
| T-2 | FD-7: Thread contract before any consumer | Features 3 and 4 are gated on it |
| T-3 | FD-3 wording ("QuickBooks") | Generalized to every destination |
| T-4 | Money-lines defaults (deposit-level, cash basis, file-only bank reads, "not ours to explain") | Superseded: accrual postings at the constrained expected entitlement (ruling 30), as `summary-journal` category totals by default (ruling 28); the bank is read from the destination (ruling 5); "accounting system's" labels |
| T-5 | Two systems reconciling the bank | **Resolved** by ruling 5: openPractice reconciles deposits only |
| T-6 | R-2: tier 3 versus Principle II | Avoided by explicit release; not resolved |
| T-7 | Retention change versus hard delete | **Resolved** by ruling 9; dependency note in Decision 3 |
| T-8 | Principle V and in-process ERPNext calls | **Resolved for the MVP** by ruling 8 (REST only). In-process use stays conditional on counsel (Open Question 2). |
| T-9 | BAA wording versus a practice-operated destination | **Resolved** by ruling 6 |
| T-10 | openChart cannot yet supply billable facts | Manual entry (ruling 21); the encounter capability stays open in openChart |
| T-11 | Interoperability brainstorm direction | Reversed for administrative data |
| T-12 | "First staged proposal" recommendation | This change departs from it, as recorded in `proposal.md` |
| T-13 | **Tier 4 beyond FD-5's list.** FD-5 reserves tier 4 for refunds, material adjustments and merges. This change adds hard delete (an irreversible identity change) and `customer-detail` enablement (an irreversible PHI disclosure). | **Conscious exception** (ruling 12) |
| T-14 | **Tamper-evident audit versus removal of audit values** (PR-10 versus PR-13) | Mechanism chosen in planning (Decision 3) |
| T-15 | `define-customer-financial-data-boundaries` called the summary journal the baseline posting route, while this change once made `customer-detail` the default | **Resolved** by rulings 26 and 28. Posting modes belong to this contract, and `summary-journal` is the default in both changes. `customer-detail` is an opt-in, allowed only after the BAA gate and before a destination's first release (AI-09). That change's design §4 cites this rule. |
| T-16 | That change's accounting projection lists a "scoped payer relationship" for a customer | **Interpretation.** In this contract the payer relationship is carried by invoicing the payer customer. A payer field on a guarantor-account customer needs a recipient-profile entry (AI-10). The schema enforces this: a `guarantor_account` customer cannot carry `payer_id` or `payer_name`, only a `profile_fields` entry. |
| T-17 | **Sign-offs below tier 4.** FD-5 relies on reversibility and delay below tier 4. This change keeps explicit sign-offs for: `link_existing` confirmation, `create_new` and revocation requests, and identity-review resolution (EB-04, EB-05); accepting or declining a correction request and re-asserting a rejected date of birth (EB-08, EB-09); export-batch release (AI-06); and the EB-17 attestation. | **Conscious exception** (ruling 12, R-2, re-review N5). Signers follow ruling 33. |

## Risks / Trade-offs

- **[PHI in a third party's books]** → Mitigated by:
  - `summary-journal` as the default, which sends no customer record or PHI (ruling 28);
  - customer records only in `customer-detail` mode behind the BAA gate, with minimum-necessary fields under a versioned recipient profile, and neutral labels;
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
5. Feature 3 (accounting contract, banks and the ERPNext adapter, Slice 4), after step 4. The remote ERPNext topology over REST is used for acceptance. Destinations run `summary-journal` by default. A destination that opts into `customer-detail` sets it before the first release, only after the BAA gate or attestation. Go-live waits for the accountant's approval of the revenue-recognition policy (ruling 30).
6. Feature 4 (clinical-fact consumption, Slice 3), after the Thread contract. Manual entry applies while the EHR reports no billable-facts capability.
7. Post-MVP: QuickBooks Online and Business Central adapters, after vendor verification.
8. Rollback disables a destination or link and holds undelivered work. It never deletes postings, audit records or links.
9. Archive only after the features land, the root pin bump lands, and `closure-evidence.md` is complete.

## Open Questions

Every remaining question is routed in `tasks.md` §3.5 to the feature or gate it blocks.

1. **Appointment authority (R-7).** Closed by ruling 31: openPractice owns the administrative appointment, and the EHR owns the encounter. Appointment events wait for a later MINOR version.
2. **Counsel on same-site in-process ERPNext calls.** Covers GPL-3.0 ERPNext and Principle V. Blocks only a future in-process adapter change; not the MVP.
3. **Frappe version policy** for co-installing ERPNext on the same bench. Blocks same-site deployment support in Feature 3.
4. **Legacy collections account.** The bookkeeper's call. The legacy share posts to a mapped account (BAB-08). Blocks Feature 3's first release for a practice with legacy run-off.
5. **Mode continuity procedure** (opening-AR true-up). Blocks any mode change after first release (AI-09). Not part of the MVP.
6. **R-2** (tier 3 versus Principle II). Avoided here, because release is explicit. Blocks any future automatic batch release.
7. **Two receivable routes in one destination** (rulings 24 and 26). How do medical-billing receivables and membership or retail receivables coexist in one destination? This covers customer namespace collisions, a person appearing both as a guarantor-account customer and as a commerce customer, and reconciliation across the two routes. It is owned by change `define-customer-financial-data-boundaries` (on branch `define-customer-financial-data-boundaries`, not yet on main), which routes those obligations and references this contract for posting. It blocks Feature 3's go-live in a destination that also carries membership or retail customers, or whose explained deposits fund their balances. Ratifying that change does not by itself lift the block. The block lifts only when that change is ratified and Feature 3 has accepted evidence for the single deposit poster with `other_route_share` (BAB-14) and the offering-route check before charge finalization (BAB-16).
8. **Telling withdrawals apart for claims already sent** (F-4). With two codes, openPractice cannot tell from a retraction whether a diagnosis was refuted or moved to the problem list, or whether a withdrawn encounter took place but can no longer be published or was entered in error. Both can matter to a claim already sent: a corrected claim against a voided one. Any code that told them apart at encounter level would reveal a restricted act, because a restricted act always withdraws the whole encounter (openChart-spec `define-clinical-acts-foundation`, design call CD-36). Until the owner decides otherwise, staff choose the claim action. The owner.
9. **The PROPOSED follow-up calls F-1 to F-4**, including whether to keep the message name `clinical_fact.entered_in_error` for every retraction or add a new name later. The owner.

## Follow-up of 2026-11-15 (ruling 93)

This change was merged to `main` as an unratified draft. On 2026-11-15 the owner accepted rulings 87 to 89 and 93. Ruling 89 adds a retraction reason code "now, while 1.0 is still unratified", so the contract is edited in place and keeps the name `openpractice-ehr-clinical-facts/1.0`; the digest identifies the bytes. `Status` stays `draft`, and `owner-ratification.md` stays PENDING.

| What changed | Why | Where |
|---|---|---|
| `retraction_reason` on `clinical_fact.entered_in_error`: an open code with two defined values, `entered_in_error` and `withdrawn` | The publisher used "entered in error" for retractions that were not entered in error (openChart-spec `define-clinical-acts-foundation`, tension CT-16); ruling 89. The two codes are the smallest list the publisher's PB-03 needs without revealing a restricted act (its CD-36) | EB-15, clinical-facts schema |
| Confirmed diagnoses only, and refuted ones retracted | Ruling 87; nothing here said otherwise, so the wording is aligned | EB-13, schema `condition` |
| Retractions accepted after a link ends | Ruling 88; the `link_id` note and EB-13's limit to linked patients could be read to refuse them | EB-15, EB-13, schema `link_id` note |

**Proposed follow-up calls (for the owner).**

| # | Call | Where |
|---|---|---|
| F-1 | **PROPOSED.** Two codes only, as the publisher asked: `entered_in_error` for exactly one Condition or Procedure entered in error while its encounter stays published, and `withdrawn` for every other retraction. The schema refuses `entered_in_error` on a retraction that names an Encounter or more than one resource, so every encounter-level retraction reads the same. No code for a refuted diagnosis is added; see Open Question 8. | EB-15, schema |
| F-2 | **PROPOSED (revised in fix round 1 and the confirmation pass).** `retraction_reason` is an open code with two defined values, so a code a later MINOR version defines stays schema-valid and EB-03 needs no exception. An undefined code is applied and recorded as `withdrawn`, and is never refused, because a refused retraction would leave a withdrawn fact billable. Its value appears in no record, log, diagnostic or stored copy of the message. It is reported only in aggregate: one integration exception per period, holding a count and no identifier or value, routed only to the integration operator, who checks for a new MINOR version or tells the publisher it is sending codes outside the contract. The period is a policy value, PROPOSED as one calendar day. The trade-off: the schema no longer refuses a code that reveals a cause. The non-leak guarantee then rests on the publisher (its CD-36), on the rule that publishers send only defined codes, and on openPractice keeping no undefined value. The `entered_in_error` constraints stay enforced in the schema. | EB-15, schema |
| F-3 | **PROPOSED.** The message keeps its name, and `retraction_reason` is optional: an absent value means `withdrawn`. A publisher's re-pin is mechanical: a fact-level retraction of one Condition or Procedure entered in error adds `entered_in_error`; every other retraction adds `withdrawn`, or may send none. | EB-15, schema |
| F-4 | **PROPOSED.** Both codes have the same billing effect: the facts stop being billable, unfinalized charges are held, and staff choose a corrected or voided claim for a claim already sent. | EB-15 |
| F-5 | **Fix round 1.** A retraction applies only to facts openPractice received under the same `link_id` and subject; any other reference changes nothing and raises an integration exception. This is a safety rule, not a design choice the owner must make. | EB-15 |

| Contract | Digest before | Digest after the follow-up |
|---|---|---|
| `openpractice-ehr-clinical-facts/1.0` | `6b2071fe61693d144aa91f4fc68e51d0cd7563d66b5cd2086f281a959f7e69ec` | `3aac773e9e8a8a370e1a78c845e5837d059b60146e411d973e0c54464aad12db` |
| `openpractice-ehr-patient-link/1.0` | `76f465e144893000de4bd2240adb9f547899e2e8100315f698c3cc6cb885b802` | unchanged |
| `openpractice-accounting-integration/1.0` | `353492ceac5bfa9a90fa7eea221c5727a303453e6232430847224c0807007885` | unchanged |

Every message valid before stays valid: the new field is optional, and is refused only on a notification that is not a retraction, as a value that is not a lowercase code, or as `entered_in_error` on an encounter-level or multi-resource retraction.

## Corrections from the 2026-11-15 spec critique

On 2026-11-15 the owner asked for the owner-supplied spec critique dated 2026-11-15 ([`ideation/review/openpractice-spec-critique-2026-11-15.md`](../../../ideation/review/openpractice-spec-critique-2026-11-15.md), added to the repository under ruling 111) to be worked through, and the same day accepted the recommendations that came out of it as rulings 99 to 111. Rulings 103 and 107 govern this change and are recorded in the Owner rulings table above. The critique was written against `main` at `0a82fc2`, before the ruling 93 follow-up, and each claim was checked against the text as it now stands. These corrections edit this unratified draft in place. Where a ruling's words decide something, the text states it as ruled; anything further is a design call under it, marked PROPOSED. `Status` stays `draft`, and `owner-ratification.md` stays PENDING.

| # | Correction | Critique item | Where | What stays open |
|---|---|---|---|---|
| SC-1 | Cross-branch caveats made true: `define-customer-financial-data-boundaries` and the domain framework are cited as merged on `main` | D.1 | "Sources" above; `proposal.md` "Framework document"; `tasks.md` Open Question 7 routing | Nothing |
| SC-2 | One party per real person or organization: the patient record, guarantor, subscriber, contact and representative are roles of, or links to, a party; a person who later becomes a patient keeps the same party; a human roster entry references one party; agents and integrations are not parties; the commerce customer role follows ruling 32; a staff member who is also a patient follows ruling 107 | B.1 | PR-15 (new); change `define-actors-agents-and-work-inbox` AM-01; one cross-reference sentence in `define-customer-financial-data-boundaries` `design.md` | F-6 and F-7 (PROPOSED). Table and DocType names are planning matters. How own-record actions are reviewed in a one-person practice belongs to the readiness change of ruling 107 |
| SC-3 | One money format (ruling 103): decimal text plus an ISO 4217 code on the wire, whole cents in storage, for every `openpractice-*` contract. The critique cites AI-03 for the format; AI-03 is the ERPNext-only adapter rule, and until now the format lived only in the accounting schema's `money` definition, with no requirement stating it. The bank-lines result carried amounts with no currency code, so it gains a required `currency`, the one schema change here | B.2 | BAB-18 (new); AI-02; accounting schema | F-8 to F-10 (PROPOSED) |

**Proposed calls under the corrections (for the owner).**

| # | Call | Where |
|---|---|---|
| F-6 | **PROPOSED.** PR-15's wording of ruling 32: roles and links instead of second person records, search before create showing parties that hold other roles, deactivation and hard delete acting on the patient role while a party keeps its other roles, and no automatic merge. | PR-15 |
| F-7 | **PROPOSED, under ruling 107.** Work on a record where the actor's own party holds a role is decided by another roster entry that holds the grant; where none exists, ruling 107's one-person alternative lets the person act, recording the role used and the own-party fact. | PR-15 |
| F-8 | **PROPOSED, under ruling 103.** Version 1.0 of every contract supports USD only, with two decimal places; another currency needs a governed MINOR version stating its minor-unit digits, and a currency with no minor unit carries no decimal point. | BAB-18, accounting schema |
| F-9 | **PROPOSED, under ruling 103.** Each contract states its rounding once. For the accounting contract: an amount openPractice computes is rounded once to the cent, half away from zero, and every total is the sum of rounded amounts. | BAB-18, AI-02 |
| F-10 | **PROPOSED, under ruling 103.** One currency per batch and per message that holds several amounts; a batch with a second currency is refused at release. | BAB-18, AI-02, accounting schema |

**Critique items answered elsewhere.**
- **A** (the revenue-cycle core): ruling 110, "Yes. Its scope and decisions will follow as their own list." It is new work, not drafted here.
- **C.1** (readiness and one person holding several authorities): ruling 107. Its readiness checklist and starter policies go in a new change, `define-practice-readiness`, by another author. PR-15 uses only the parts of ruling 107 that decide what happens when a staff member is also a patient.
- **D.1**, second half, and **D.2**: ruling 108, the owner drafts a framing-decisions v2 that folds in rulings 1 to 98 with an index of every ruling, and version 1 stays as history. Ruling 109: nothing is promoted to canonical specs before building; the owner ratifies first, and each change is archived after its feature lands.
- **B.3**, **C.2** and **C.3** are answered in `define-actors-agents-and-work-inbox` and `define-patient-gateway-and-escalation`.

| Contract | Digest before | Digest now |
|---|---|---|
| `openpractice-accounting-integration/1.0` | `353492ceac5bfa9a90fa7eea221c5727a303453e6232430847224c0807007885` | `a244a6df01f93924b456c5cb04ec6abf7870f71d5c9087b8415e89a2cd3af988` |
| `openpractice-ehr-patient-link/1.0` | `76f465e144893000de4bd2240adb9f547899e2e8100315f698c3cc6cb885b802` | unchanged |
| `openpractice-ehr-clinical-facts/1.0` | `3aac773e9e8a8a370e1a78c845e5837d059b60146e411d973e0c54464aad12db` | unchanged |

The accounting change is one tightening: a bank-lines result must now carry `currency`. No consumer outside openPractice cites that digest.
