Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Administrative Deltas Synthesis — Combined Read for openPractice

Recombination of the nine atomic administrative deltas into themes that
shape openPractice scope and sequencing.

## Theme 1 — Platform-inherited operations are free, but not differentiating

ERP backbone, asset management, report builder, API surface, and the
DocType framework all arrive by riding Frappe/ERPNext. openPractice
gets them close to free if it sits on the Frappe stack, which argues
for the stack — but none of them differentiate the product. Every
Frappe-based competitor (including Marley itself) has them. Spend the
resulting saved effort on the themes below instead.

## Theme 2 — Revenue cycle is the real administrative fork

The patient-as-customer delta (Marley uses ERPNext customer invoicing)
and the US-claims delta (OpenEMR's X12 5010, clearinghouses, UB-04,
ONC certification) are two ends of the same decision: does openPractice
bill US insurance, or not? If yes, nothing on the Frappe side ships
today; a claims pipeline becomes the single largest administrative
build. If the first market is Puerto Rico private-pay / cash clinics
and labs, Marley-style invoicing is sufficient and the claims delta
can be deferred. This decision gates the milestone plan.

## Theme 3 — Facility scope: outpatyou Outpatient-first defers two deltas

Inpatient/ward operations and pharmacy inventory both assume facility
scale. If openPractice v1 targets outpatient practices, both move to
post-v1. If it targets clinics-with-beds or labs from day one, service
units and stock workflows are v1 scope. The deltas document what
Marley already provides, so borrowing beats greenfield building here.

## Theme 4 — The pharmacy delta sits on the clinical boundary

Dispensing is where administrative inventory tooling causes clinical
harm when it fails. Keep inventory mechanics in openPractice scope,
but the interaction-checking and prescriber-facing flows belong to
openChart. The cross-packet link from openPractice-pharmacy to
openChart e-prescribing should be an explicit dependency, not an
afterthought.

## Theme 5 — Customization depth is a moat for buyers, not builders

The DocType-framework and report-builder deltas mean customers and
implementers can extend openPractice without forking. That is a
distribution feature as much as a technical one: it is how OpenEMR's
implementer ecosystem works, and it is a structural reason to stay on
Frappe rather than build a bespoke core.

## Open questions to resolve before staging

- Target first market: PR/private-pay vs US insurance (sets Theme 2).
- Facility scope for v1: outpatient-only vs clinic-with-beds (Theme 3).
- Pharmacy: module now or integration point later (Theme 4).
