# Medical Billing, Commerce, And Accounting Routing — Brainstorm

Status: brainstorm
Kind: architecture
Summary: insurance claims and healthcare receivable lineage remain in medical billing while memberships and retail use ordinary customer invoicing and all paths reconcile to connected accounting without duplicate obligations or revenue.
Topics: openpractice-customer-financial-separation, financial-control, patient-billing, claims, practice-structure
Repository context: openPractice — financial ownership across medical billing, commerce, subscription billing, and connected accounting
Captured: 2026-11-14

## Possible feats

- **Offering-specific billing routes** — distinguish insurance claims, direct healthcare charges, memberships, and retail sales.
- **One obligation across systems** — identify the authoritative receivable and prevent a mirror invoice from creating another debt.
- **Customer-aware accounting bridge** — support necessary named customer invoices alongside the existing deposit-level journal export.

## Focus

Which domain creates, collects, adjusts, and reports each obligation when a
practice combines insurance reimbursement, direct payment, memberships, and sales?

## Proposed ownership

| Domain | Owns | Consumes or references |
|---|---|---|
| Connected EHR | Clinical encounters, signed notes, diagnoses, orders, medications, results | Administrative patient linkage |
| openPractice medical billing | Charge/claim lineage, insurance adjudication, healthcare patient responsibility and allocation | Minimum necessary coded service facts and clinical evidence references |
| CRM / commerce | Customer relationship, offer/campaign history, orders and product/service selections | Purpose-limited identity and permitted financial status |
| Subscription / accounting application | Membership agreements, renewal invoices, ordinary sales invoices, credits and commercial receivables | Customer identities and governed service/product catalog |
| Connected accounting / ERP | General ledger, accounting recognition, taxes, expenses, payroll and final statements | Authoritative invoices and reconciled healthcare financial postings |

These are logical owners, not a requirement for five products. Membership
billing may be an accounting module. Product inventory, subscription scheduling,
and general accounting remain connected capabilities; openPractice does not
become a general ERP or author a clinical chart.

## Offering and routing contract

Extend the administrative service catalog with an effective-dated financial
classification: offering identity, selling legal entity, route, authoritative
obligation system, product/service category, currency, applicable tax treatment,
membership entitlement reference, accounting mapping, and disclosure profile.
Tax and accounting policies come from configured, reviewed rules rather than
being inferred from a product name. Membership terms and payment mandates are
different records.

| Route | Obligation owner | Key boundary |
|---|---|---|
| Insurance-billed healthcare | Medical billing | Uses required medical codes; a claim is not a second customer invoice |
| Direct-pay healthcare | Medical billing healthcare receivable | No claim is required; existing self-pay, GFE, receipts and patient-billing controls still apply |
| Membership subscription | Subscription/accounting subledger | Terms, beneficiaries, renewal and payment authorization; no claim merely because the buyer is a patient |
| Retail or separately sold service | Commerce/accounting subledger | Ordinary sales line, price, applicable tax and refund; no invented medical encounter |

A medical membership or cash-pay healthcare service can contain PHI. Route and
regulatory classification are independent. A generic retail purchase is not
automatically PHI, and care linkage must not be copied into its customer record
without a defined purpose. Every mixed order is routed at line level.

Membership benefits must identify whether a service is included, discounted,
separately chargeable, or potentially insurance-billable under approved terms.
Unresolved entitlement or payer restrictions create owned review work before
financial posting; they never silently generate both a covered membership charge
and an insurance charge for the same service. This boundary defines no insurance
coverage or membership-contract legal rule.

## Reconciliation without duplication

Every obligation and economic event carries its source domain, immutable source
identifier, legal entity, currency, effective/version context, and destination
mapping. External posting acknowledgements map to that same source event.
Retries reuse its identity. Corrections are linked reversals and replacements.

The first deployment must choose one posting route per event:

- **Summary journal route:** the medical subledger owns detail and accounting
  receives the existing reconciled deposit-level journal and certificate.
- **Customer-detail route:** accounting receives necessary customer financial
  detail; any imported invoice representing a medical receivable is a linked
  mirror or explicit accounting recognition of that receivable, not a new
  collectible obligation.

The same revenue or receipt must not arrive once through a customer invoice and
again through an additive summary journal. Cash, receivables, deferred revenue,
recognized revenue, tax liabilities, refunds and fees remain distinct. A renewal
invoice, its payment, and its revenue recognition are not three sales.

For mixed payments, allocate one processor receipt across the obligations and
reconcile its gross, fees and net settlement once. A combined customer view is
a projection; it does not merge legal entities, guarantor authority or merchants.
Preserve legacy run-off exclusions, as-of reporting, FD-3 export-batch freeze,
and FD-5 financial authority. No new money movement is authorized by this packet.

FHIR's [Invoice boundaries](https://www.hl7.org/fhir/R5/invoice.html) provide a
reference for distinguishing provider invoicing from insurer claims. Concrete
integration versions and accounting recognition policies remain to be selected.

## Alternatives and tensions

Summary-only accounting minimizes detail but cannot alone support named customer
invoicing. Detailed integration improves customer accounting while increasing
protected data and reconciliation responsibility. The captured default assumes
customer identity is present; summary journals remain a valid posting option,
not a promise that accounting contains no PHI.

## Open questions

- Which accounting/subscription application and detailed or summary posting route are selected first?
- How are membership entitlements coordinated with insurance and direct-pay services for that practice?
- Which subscription and retail functions are in the first delivery versus connected later?

## Relationships

Refines [Financial Control And Accounting Bridge](openpractice-small-practice-beta-financial-control-and-accounting-bridge.md),
[Money Lines And Bank Inbox](openpractice-small-practice-beta-money-lines-and-bank-inbox.md),
[Self-Pay And GFE](openpractice-small-practice-beta-self-pay-and-gfe.md),
and [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md).
Joins [Identity](openpractice-customer-financial-separation-identity.md)
and [Business Views](openpractice-customer-financial-separation-business-views.md)
in [Customer To Books](openpractice-customer-financial-separation-synthesis-customer-to-books.md).
