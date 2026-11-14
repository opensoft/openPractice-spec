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

## Goals / Non-Goals

**Goals:** define domain ownership, minimal interoperable customer records,
financial routing and recipient-aware projections that can be tested without
selecting a particular accounting vendor or adding clinical authority.

**Non-Goals:** implement applications, create a universal unrestricted customer
database, replace external accounting, select a legal membership model, authorize
marketing or money movement, amend the constitution, or enlarge beta acceptance.

## Decisions

### 1. Extend the party register with role-specific links

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
refunds, balances and source-event references. Inclusion still requires a
defined purpose and destination profile.

Default exclusions are chart notes, diagnoses, medication lists, results,
full insurance claims and EHR medical-record numbers. Medical billing may retain
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
workspaces.

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

Preserve the existing deposit-level medical summary journal as the baseline
posting route. Customer identity can still exist in accounting through ordinary
customer/membership records. A connector can select a detailed medical customer
route under an explicit reviewed mapping, but it must not add the same effect
through both routes.

Source domain, immutable obligation/event identifier, entity, currency, version
and destination mapping correlate invoices, payments and postings. Detail
representations in accounting refer to the same medical receivable; they cannot
produce a second collectible balance. Retries reuse event identity. Transfers
between domains are linked reclassifications, never independent sale events.

Separate invoice issuance, cash collection, deferred revenue, recognized revenue,
tax liabilities, refunds and fees. Mixed receipts allocate to compatible
obligations, then reconcile one gross-to-net processor settlement. Preserve
legacy run-off exclusions and entity/merchant boundaries. Corrections append
reversals; exported batches remain immutable under FD-3. Existing patient
financial authority and payment-mandate controls remain in force under FD-5.

### 5. Reuse purpose-bound views and the existing Roster

Define a versioned recipient profile: actor/organization, job, purpose,
relationship and row scope, field allowlist, source provenance, sensitivity,
output channel and applicable handling/retention obligations. Cover attachments,
logs, exports and connector diagnostics as well as structured API fields.
Unresolved profiles or unsupported destination safeguards refuse delivery.

Use existing shipped role-recipe mechanics. Accountant, outsourced biller,
commerce operator and clinician can have different views even at one firm.
No financial or customer permission confers chart access. Required vendor/BAA
arrangements and revocation checks apply to the actual service and data.
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
