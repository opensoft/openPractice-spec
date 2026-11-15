# openPractice Customer And Financial Separation Overview — Brainstorm

Status: brainstorm
Kind: reference
Summary: assume connected accounting contains a limited protected customer and financial record while assigning clinical truth, medical claims, commercial obligations, and business views to explicit owners.
Topics: openpractice-customer-financial-separation, practice-strategy, financial-control, platform-security
Repository context: openPractice — review of existing spec-leg design and capture of the owner's customer/accounting boundary direction
Captured: 2026-11-14

## Possible feats

- **One business with bounded domains** — operate care, memberships and sales with useful customer records and controlled clinical access.
- **Explicit accounting data contract** — retain necessary named financial detail while minimizing patient information transferred to accounting.
- **Reconciled business intelligence** — explain customer and offering revenue across domains without duplicate recognition or uncontrolled outreach.

## Motivation

The owner asked whether outsourced accounting would contain PHI, then described
a practice selling memberships, supplies and other services alongside insured
care. Summary financial totals alone do not support all customer operations.
The final working assumption was to keep customer identity in accounting and
minimize unnecessary patient information, accepting that the remaining medical
financial relationships can still be PHI.

This packet records that direction and proposed contracts. It remains
non-normative brainstorm material. Its companion [OpenSpec proposal](../../openspec/changes/define-customer-financial-data-boundaries/proposal.md)
contains proposed requirements, not implemented capabilities or a claim of
deployment compliance. No runtime or vendor is selected here.

## Review of what exists

The review focused on the feature-list, small-practice work model, financial
bridge, service catalog, self-pay, money, identity, access, communications,
analytics and existing reimbursement-control proposal. Competitive product
claims in older research were not re-audited.

| Existing material | Finding | Treatment in this update |
|---|---|---|
| [Registration](openpractice-feature-list-registration-and-demographics.md) and [party substrate](openpractice-small-practice-beta-feature-normalization.md#this-is-a-work-model-not-a-domain-model) | Patient, subscriber, guarantor and representative roles are already distinguished; commerce customer and membership roles are not explicit. | Extend the existing party model and protect cross-system links. |
| [Financial bridge](openpractice-small-practice-beta-financial-control-and-accounting-bridge.md) | Correctly keeps healthcare receivable lineage here and general accounting external, but does not define named accounting customer records. | State the limited protected customer/financial record assumption and field contracts. |
| [Money export](openpractice-small-practice-beta-money-lines-and-bank-inbox.md#the-export-and-the-only-lock-in-the-product) | Deposit-level cash-basis journals and frozen export batches are concrete; customer invoicing introduces another possible posting path. | Retain that route and require an explicit alternative detail route with no additive duplication. |
| [Self-pay](openpractice-small-practice-beta-self-pay-and-gfe.md#6-where-each-dollar-lands) | Retail/supplement income appears in the reason taxonomy; recurring membership agreements, entitlements and billing ownership do not. | Separate offering routes and external subscription/commerce ownership. |
| [Service catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md) | Offering and entity identities exist, but insurance, direct-pay, membership and retail routing are not specified. | Add effective-dated routing, obligation owner and disclosure profile as proposed contracts. |
| [Security](openpractice-feature-list-platform-security-and-compliance.md) and [Roster](openpractice-small-practice-beta-roster-access-and-offboarding.md) | Administrative/financial PHI and external biller controls already exist. | Explicitly cover accountant and commerce projections without granting chart access or introducing a policy editor. |
| [Analytics](openpractice-feature-list-reporting-and-analytics.md) and [communications](openpractice-feature-list-patient-engagement-and-communications.md) | Versioned financial metrics and purpose/channel decisions are useful foundations; cross-domain commercial revenue and outreach boundaries need definition. | Add controlled business views and separate analysis, export and campaign-use decisions. |
| [Reimbursement controls](../../openspec/changes/add-reimbursement-retention-control/proposal.md) | Authorization, external reconciliation and retention are separately proposed. | Preserve those boundaries and its separate lifecycle; do not claim this update ratifies it. |

Two pre-existing documentation discrepancies need later reconciliation: the older
financial-bridge atomic still describes a period-freeze ceremony that FD-3 and
the later Money Lines document replaced; the reimbursement-control design calls
the constitution a placeholder, while the assembly's current
`.specify/memory/constitution.md` records v1.0.0 ratified 2026-08-24. Some older
constitution links also assume the pre-Triad directory layout. This update
does not rewrite those historical documents or infer a new ratification.

## Goals

- Keep necessary customer identity and financial detail available to accounting.
- Maintain EHR authority and restricted access for clinical records.
- Distinguish insurance claims, healthcare receivables and ordinary customer invoices.
- Support membership and retail business analysis without duplicate revenue.
- Treat patient-linked financial data and its outputs as protected where applicable.

## Non-goals

No application implementation, vendor selection, new general ERP, clinical
chart replica, inventory engine, legal determination, automatic marketing
permission, constitution amendment or expansion of beta release commitments.
General-ledger integration is not assumed PHI-free. Moving data between
applications does not establish a hybrid entity or de-identification.

## What the system delivers

The intended behavior is one correlated business relationship with different
views for the accountant, biller, operations staff, clinician and owner. The
accountant receives the customer identity, invoices and payments needed for
the job. Insurance billing receives necessary claim data. The EHR keeps the
clinical record. Analytics combines selected financial and customer events.

## System model

The [Customer To Books synthesis](openpractice-customer-financial-separation-synthesis-customer-to-books.md)
contains the domain/data-flow diagram and the end-to-end failure paths.
Logical separation and PHI classification are independent axes: accounting
and commerce may be protected even when chart access is excluded.

## Cluster map

- [Synthesis: Customer To Books](openpractice-customer-financial-separation-synthesis-customer-to-books.md) — identity links, obligation ownership, accounting postings and purpose-bound business views.

## How it fits

The existing [beta overview](openpractice-small-practice-beta-overview.md)
remains the broader product map. This packet supplies the current customer,
commerce and accounting boundary interpretation for later reconciliation with
that baseline. It preserves the small-practice work model, immutable posted
events, reversal corrections, entity separation and external general accounting.
It proposes no extra workspaces or arbitrary permission-matrix editor.

The [OpenSpec design](../../openspec/changes/define-customer-financial-data-boundaries/design.md)
turns these findings into domain contracts and acceptance scenarios. Any future
implementation follows the ordinary review, security and single-Speckit-feature
handoff in that change. Neither this packet nor a leg commit advances the Triad
assembly pin by itself.

## Key decisions and open questions

Captured owner direction: customer information is assumed present in accounting;
unnecessary patient detail is minimized; the remaining financial record may be
PHI; memberships use ordinary subscription/accounting functions; insurance
claims and medical codes remain in medical billing; the chart stays in the EHR.

Proposed elaborations include per-field ownership, role-specific crosswalks,
line-level offering routing, one authoritative obligation and purpose-specific
business projections. First vendors, physical deployment, field lists,
membership/insurance contract rules and release sequencing remain open.

## Document map

Overview: this document.

Synthesis:

- [Customer To Books](openpractice-customer-financial-separation-synthesis-customer-to-books.md)

Atomics:

- [Customer Identity And Patient Roles](openpractice-customer-financial-separation-identity.md)
- [Medical Billing, Commerce, And Accounting Routing](openpractice-customer-financial-separation-financial-routing.md)
- [Purpose-Bound Business Views](openpractice-customer-financial-separation-business-views.md)
