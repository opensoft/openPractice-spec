# Proposal

Status: draft

## Why

The existing openPractice design separates clinical authority and general
accounting, but does not define how named customers, memberships and retail
sales coexist with medical billing. The owner's current direction is to assume
customer information exists in accounting, retain useful financial detail, and
minimize unnecessary patient data while protecting the PHI that remains.

## What Changes

- Extend the existing party register with customer and membership roles,
  controlled links, field ownership and purpose-specific identity projections.
- Keep clinical records authoritative in the connected EHR and necessary coded
  healthcare information in medical billing; chart access does not follow
  customer, accountant or commerce access.
- Define a limited protected customer/financial record in connected accounting,
  including necessary name, billing contact, membership, invoice and payment data.
- Distinguish insurance-billed care, direct-pay care, subscriptions and retail or
  separately sold services at offering/line level. Billing route does not decide
  PHI status.
- Assign one authoritative obligation and one accounting posting route per
  economic event, preserving customer-detail and summary-journal options without
  duplicate invoices, collections or recognized revenue.
- Keep subscriptions, commercial receivables, inventory and general accounting
  in connected business applications; do not turn openPractice into an ERP.
- Define controlled business analytics and separate authorization for report
  access, export and outreach, using existing communication and Roster concepts.
- Preserve existing reversal, settlement, entity, financial-authority and
  retention boundaries; this proposal grants no new autonomous actions.
- Capture a linked review and three-tier brainstorm packet without rewriting
  the historical feature packets or expanding beta acceptance silently.

## Capabilities

### New Capabilities

- `customer-role-boundaries`: Administrative identity and role links, necessary named accounting records, field ownership, and independent clinical access.
- `financial-domain-routing`: Offering-specific obligation ownership, insurance/direct-pay/subscription/retail routing and reconciled accounting handoffs without duplication.
- `purpose-bound-business-data`: Protected financial projections, scoped vendor access, reproducible business metrics, and independent output/outreach authorization.

### Modified Capabilities

None. The current checkout has no canonical `openspec/specs/` capabilities;
the existing reimbursement-control deltas are a separate in-flight proposal.
This change refines the brainstorm architecture without editing that proposal.

## Impact

- Owns specification changes in the openPractice spec leg only; introduces no
  runtime code, database migration, external vendor change or deployment.
- Future integration contracts affect party matching, catalog routing,
  accounting customer/invoice synchronization, subscription events, medical
  receivables, settlement reconciliation, reporting and communications.
- First providers, field mappings, posting mode, membership/insurance terms and
  release sequencing remain implementation-planning decisions. A logical
  boundary does not require a new application per domain.
- Proposed requirements and scenarios are ready for normal design review;
  executable implementation tasks belong to one later Speckit feature after
  the scope and concrete contracts are accepted.

Review findings and source-document relationships are in the
[Customer And Financial Separation Overview](../../../ideation/brainstorm/openpractice-customer-financial-separation-overview.md).
