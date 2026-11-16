# Design

## Context

See [proposal.md](proposal.md) for motivation and scope. openPractice remains
a Frappe-native governance scaffold. Existing design establishes an operational
party register, healthcare receivables, immutable Money Lines, an external
general ledger, shipped Roster recipes and purpose-aware communications.
There is no application or production database to migrate in this change.

The [review packet](../../../ideation/brainstorm/openpractice-customer-financial-separation-overview.md)
maps the affected documents and preserves historical decisions. The current
assembly constitution records v1.0.0, ratified 2026-08-24; the separate
reimbursement-control design's placeholder-constitution statement is stale
and is not adopted by this change.

## Owner rulings that govern this change (2026-11-14)

Source: **Brett Heap, 2026-11-14, authoring session.** The full table is in
`define-mvp-practice-foundation` design.md; these rows govern this change. Row 193 has the source **Brett Heap, 2026-11-15, accepting the recommendations on decisions 152 to 194** ("accept your recs for 152-194"), and quotes the question and the accepted wording.

| # | Ruling | Governs here |
|---|---|---|
| 25 | Medical-billing customers may carry name and billing contact in a destination only after its BAA gate, in `customer-detail` mode only. Additional fields need a versioned recipient profile. | customer-role-boundaries |
| 26 | `define-mvp-practice-foundation` owns posting mechanics. This change owns customer and party roles, offering routes, obligation ownership and purpose-bound views. | financial-domain-routing, design §4 |
| 28 | `summary-journal` is the default; `customer-detail` is a per-destination opt-in behind the BAA gate. | design §4 |
| 32 | One Party with roles. "Customer" is a party role only for membership and retail buyers; a medical-billing accounting customer is derived from the guarantor or payer role. | customer-role-boundaries, design §1 |
| 33 | FD-5 governs sign-offs. A named human, or an opted-in named AI agent with an openWallet ID, may sign; tier 4 stays human-only. | customer-role-boundaries, financial-domain-routing, design §5 |
| 193 | (2026-11-15) Question: Some rules give "the owner" a power, such as seeing full bank account numbers or setting the self-pay discount. The roster has no owner role, and ruling 145 records owners only as a fact. Who holds those powers? Accepted: "The practice manager role. A recorded owner grants nothing by itself and is used only to check independence, as in rulings 139 and 149. Otherwise anyone able to record an owner could give themselves those powers." | Decision 1 (where this change's requirements give the practice's owner a view, the practice manager recipe holds it, and an owner link grants nothing by itself); purpose-bound-business-data's revenue scenario; `define-mvp-practice-foundation` F-13 holds the details |

## Goals / Non-Goals

**Goals:** define domain ownership, minimal interoperable customer records,
financial routing and recipient-aware projections that can be tested without
selecting a particular accounting vendor or adding clinical authority.

**Non-Goals:** implement applications, create a universal unrestricted customer
database, replace external accounting, select a legal membership model, authorize
marketing or money movement, amend the constitution, or enlarge beta acceptance.

## Decisions

### 1. Extend the party register with role-specific links

The customer role applies only to membership and retail buyers (ruling 32). For
medical billing, an accounting-side customer (a guarantor account or payer, in
`customer-detail` mode only) is derived from the guarantor or payer role and is
not a separate party role. `define-mvp-practice-foundation` PR-15 states the one-party rule as a requirement, proposed on 2026-11-15, and its AI-10 withholds a restricted guarantor's billing address and contact (design call F-11, proposed the same day). Where this change's requirements give the practice's owner a view, it is the holder of the practice manager recipe, because a recorded owner grants nothing by itself (ruling 193, in the table above; `define-mvp-practice-foundation` design call F-13 holds the details, PROPOSED).

Person identity, customer, patient, member, payer and representative are distinct
concepts. Keep effective dates, evidence, legal-entity scope and domain identifiers
on their links. A payer can differ from the beneficiary and receives only the
scope supported by that financial relationship. A nonpatient purchaser does not
need a clinical patient record.

For each synchronized field, declare its owning source, permitted update direction,
conflict behavior and recipient projection. Identity ambiguity creates review
work. A correction must preserve the financial audit trail and confidential
contact instructions. Do not assume a shared phone or email proves identity.

An unrestricted merged record was rejected because it makes every integration a
clinical disclosure. Disconnected duplicate directories were rejected because
they make customer accounting unreliable. The existing party register and a
protected crosswalk supply the necessary logical linkage without another spine.
FHIR Person is a reference pattern, not a required resource or version upgrade.

### 2. Protect useful accounting records and minimize clinical detail

Accounting is assumed to hold necessary customer identity. Its candidate
projection includes name, customer identifier, billing address/contact, scoped
payer relationship, necessary offering/membership labels, invoices, payments,
refunds, balances and source-event references. For a medical-membership record
those labels stay neutral and never reveal a service, condition or type of
care. Inclusion still requires a defined purpose and destination profile.
Writing any PHI-bearing customer record, care-linked or medical-membership,
into a destination requires that destination's BAA gate, recorded in
openPractice's accounting destination register (`define-mvp-practice-foundation`
AI-14); openPractice's offering routing enforces it.

Default exclusions are chart notes, diagnoses, medication lists, results,
full insurance claims, EHR medical-record numbers and service-revealing reason
categories. Medical billing may retain
required coded service facts and approved claim evidence under the existing
attachment/custody contracts. Those limited copies do not transfer clinical
authority. A targeted accounting exception needs documented fields, purpose,
recipient and authority; broad chart replication is outside this design.

PHI classification is a separate decision from domain and payment route.
The proposed safe operational default treats patient-linked medical financial
records as protected. Generic retail identities are not automatically patients.
Removing medical codes, tokenizing identity or exporting a small aggregate does
not establish de-identification.

### 3. Route offerings and own obligations explicitly

Extend the service catalog's financial contract with route, obligation owner,
entity, category, currency, approved tax/accounting mapping, applicable entitlement
and disclosure profile. A mixed order routes per line. These are internal
contracts rendered through small-practice defaults, not new configuration
workspaces. For an insurance-billed or direct-pay healthcare service, the offering
is the catalog item of `define-revenue-cycle-core` SC-01, which carries these facts (SC-06); that change
is a draft not yet on `main`, and the sentence in "Offerings determine financial routing at line
level" is its conforming amendment PA-4, PROPOSED until both changes are ratified.

- Medical billing owns insurance-billed care and direct-pay healthcare
  receivable lineage, preserving the existing self-pay/GFE path.
- Connected subscription/accounting capabilities own membership agreements,
  ordinary renewal invoicing, credits and their commercial receivables.
- Connected commerce/accounting capabilities own retail orders, sales invoices
  and inventory where applicable.
- Connected accounting owns general-ledger recognition and final statements.

A membership entitlement explains whether a service is included, discounted
or separately chargeable. Any interaction with insurance is governed by accepted
practice/payer terms; unresolved eligibility does not create an automatic claim
or duplicate debt. Being a member does not imply permission to charge a card.

### 4. Choose one posting path for each economic event

Posting mechanics belong to `openpractice-accounting-integration/1.0`, owned
by change `define-mvp-practice-foundation`: posting identity and idempotency,
batches and their freeze, release, retries, the reconciliation report, the
`summary-journal` and `customer-detail` modes and the BAA gate. Every
destination starts in `summary-journal`, the default and recommended setup:
balanced category totals to payer-class control accounts, with patient-level
receivable detail kept in openPractice (owner ruling 28). A destination may opt
into `customer-detail` only before its first released batch, after its BAA gate
(that change's AI-09 and AI-14). Customer
identity can still exist in accounting through ordinary customer/membership
records. No event may add the same effect through both routes.

Source domain, immutable obligation/event identifier and entity tie invoices,
payments and postings to their authoritative obligation. Detail
representations in accounting refer to the same medical receivable; they cannot
produce a second collectible balance. Transfers between domains are linked
reclassifications, never independent sale events.

Separate invoice issuance, cash collection, deferred revenue, recognized revenue,
tax liabilities, refunds and fees. Mixed receipts allocate to compatible
obligations, then reconcile one gross-to-net processor settlement. Preserve
legacy run-off exclusions and entity/merchant boundaries. Corrections append
reversals; exported batches remain immutable under FD-3. Existing patient
financial authority and payment-mandate controls remain in force under FD-5.

### 5. Reuse purpose-bound views and the existing Roster

Use the single versioned recipient-profile shape that change
`define-mvp-practice-foundation` defines (AI-23, schema
`recipientProfileDocument`): recipient (actor role, organization or
destination), purpose, relationship and row scope, source provenance, field
allowlist with each field's purpose, recipient and authority, sensitivity,
output channels and applicable handling/retention obligations. This change does
not define a second shape. Cover attachments,
logs, exports and connector diagnostics as well as structured API fields.
Unresolved profiles or unsupported destination safeguards refuse delivery.

Use existing shipped role-recipe mechanics. Accountant, outsourced biller,
commerce operator and clinician can have different views even at one firm.
No financial or customer permission confers chart access. Required vendor/BAA
arrangements and revocation checks apply to the actual service and data. Approvals,
release decisions and attestations follow the FD-5 tiers under
`define-mvp-practice-foundation` BAB-17 (ruling 33). A named AI agent with an
openWallet ID signs only opted-in action types, and tier 4 stays human-only.
The two relevant capability specs own customer-role separation and financial
outputs; this design neither rewrites nor ratifies the separate
[reimbursement authorization proposal](../add-reimbursement-retention-control/proposal.md).

### 6. Separate analysis, output and communication authority

Analytics consumes authorized identity links and selected financial/customer
events, not a chart replica. Metrics distinguish invoiced, collected, recognized
and net revenue; versions specify categories, refunds/fees, source events,
periods and exclusions. Campaign attribution is not a claim of causal lift.

Computing a cohort does not grant permission to export it or contact it. Existing
communication decisions evaluate the current recipient, purpose, channel,
preferences and any required authorization or applicable exception at send time.
Generic marketing opt-in does not replace required HIPAA authorization. Do not
infer outreach rights from a retail label or the existence of an accounting
customer. Vendor reuse for its own promotion requires a separate applicable basis.

## Risks / Trade-offs

- [Identity links disclose care relationships] → Protect the crosswalk and each projection independently.
- [Medical plus commercial invoices double-count revenue] → One authoritative obligation and one selected posting path per event, with reconciliation scenarios.
- [A connected ledger writes or renews PHI-bearing records outside openPractice] → A configuration-time BAA gate on PHI-bearing offerings, plus a deployment control recorded in the risk analysis.
- [Membership benefits overlap insured services] → Explicit entitlements and approved terms; unresolved financial routing creates owned review work.
- [Accounting integrations leak PHI in attachments or logs] → Destination allowlists and handling controls cover every output surface; reject unsupported delivery.
- [A joined business view expands access] → Recheck row, relationship, purpose and output permissions; broader reports use reviewed aggregates or de-identification.
- [Scope grows beyond the small-practice beta] → Separate architectural contracts from release sequencing and connected-product selection.

## Migration Plan

This delivery changes documentation only. Leave pinned spec/code checkouts and
runtime untouched; review and land spec-leg work through the normal Triad flow.
Before future implementation, reconcile affected legacy design passages,
select concrete field/connector profiles, and hand off once to Speckit.

A future deployment links existing customers using verified identifiers,
reviews ambiguous matches, reconciles opening balances and runs the selected
posting path in comparison mode before enabling delivery. Rollback disables
the affected connector or projection without deleting posted events, source
links, retention obligations or reconciliation evidence. An uncertain external
payment must be reconciled before a non-idempotent retry.

## Open Questions

- Which accounting, subscription, payment and CRM providers instantiate these contracts first?
- Which exact fields and contact-update directions does each selected connector require?
- Which membership benefits and approved insurance interactions are in the first deployment's catalog?
- Which membership/retail and analytics scenarios enter the first release under a separate sequencing decision?

These are deployment or sequencing choices within the stated boundaries; they
do not authorize a different identity, clinical or obligation-ownership model.
