Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# openPractice Administrative Deltas — Marley Health vs OpenEMR (Overview)

Whole-system lens over the administrative capability deltas between
Marley Health (Frappe/ERPNext health app, formerly Frappe Health) and
OpenEMR, captured from the 2026-11-27 competitive analysis. Each delta
was classified strictly administrative or clinical; administrative
deltas live in this packet, the clinical counterpart packet lives in
openChart-spec at anchor `openchart-clinical-delta-marley`.

All deltas are unverified working claims — research notes from a survey
of upstream documentation, not staged requirements. Verify against
upstream before any document leaves `Status: brainstorm`.

## Delta inventory

| # | Delta | Lead | Doc |
|---|-------|------|-----|
| 1 | ERP backbone (accounting, buying, stock) | Marley/ERPNext | [erp-backbone](openpractice-admin-delta-marley-erp-backbone.md) |
| 2 | Metadata-driven DocType framework | Marley/Frappe | [doctype-framework](openpractice-admin-delta-marley-doctype-framework.md) |
| 3 | Inpatient & ward operations | Marley | [inpatient-ward-operations](openpractice-admin-delta-marley-inpatient-ward-operations.md) |
| 4 | Pharmacy & supplies inventory | Marley | [pharmacy-supplies-inventory](openpractice-admin-delta-marley-pharmacy-supplies-inventory.md) |
| 5 | Asset & equipment management | Marley/ERPNext | [asset-equipment-management](openpractice-admin-delta-marley-asset-equipment-management.md) |
| 6 | Uniform API surface | Frappe | [api-surface](openpractice-admin-delta-marley-api-surface.md) |
| 7 | Report builder & dashboards | Frappe | [report-builder-dashboards](openpractice-admin-delta-marley-report-builder-dashboards.md) |
| 8 | Patient-as-customer revenue flow | Marley/ERPNext | [patient-as-customer-billing](openpractice-admin-delta-marley-patient-as-customer-billing.md) |
| 9 | US claims pipeline & ONC certification | OpenEMR | [us-claims-certification](openpractice-admin-delta-marley-us-claims-certification.md) |

## Pattern

The Marley-side deltas are platform-inherited operational capabilities
(finance, logistics, ward logistics, customization, integration). The
single OpenEMR-side administrative delta is the US regulatory and
claims stack — the one area where "administrative" is genuinely
strategic for a US-facing product. See the
[synthesis](openpractice-admin-delta-marley-synthesis.md) for the
combined read and its implications for openPractice scope.

## Cross-references

- Clinical packet: openChart-spec, anchor `openchart-clinical-delta-marley`
- Existing related packet: `openpractice-customer-financial-separation-overview.md`
  (identity across clinical and financial domains)

## Primary sources

- Marley / Frappe Health: https://marley.health and https://github.com/earthians/marley
- OpenEMR feature inventory: https://open-emr.org and ONC certification listing
- Frappe licence/trademark context: https://docs.frappe.io/legal/others/license-and-trademark
