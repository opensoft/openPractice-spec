Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Delta A4 — Pharmacy & Supplies Inventory

## What the capability is

Stock-level management of medications and consumables: batches and
expiry tracking, dispensing against prescriptions, warehouse/locations,
material requests between units, and automated reorder levels.

## How the leader implements it

Marley links its Drug/Item records to ERPNext Stock Items, so
dispensing posts stock entries with batch/expiry, and replenishment
uses ERPNext material requests and reorder logic. Consumables used in
procedures can decrement stock the same way.

## Where the other system stands

OpenEMR's in-house dispensary mode is a thin wrapper over its fee/billing
structures — adequate for a small office dispensary, but no batch/expiry,
no warehouses, no reorder automation.

## Implication for openPractice

Inventory mechanics inherited from ERPNext — decision is scope, not
capability (see Synthesis Theme 3). The clinical boundary matters:
stock mechanics belong here in openPractice; interaction checking,
allergy screening, and prescriber decision support belong in openChart
(cross-ref `openchart-clinical-delta-marley` e-prescribing atomic).
Dispensing is where admin tooling can cause clinical harm, so any
openPractice dispensing flow must consume openChart's safety checks
rather than reimplementing them.

## Build / adopt options

- v1 outpatient: expose an optional Pharmacy module backed by ERPNext
  Stock; document it as facility-scale.
- Later: closed-loop medication admin for inpatient if Theme 3 lands.

## Verification links

- ERPNext Stock: https://docs.frappe.io/erpnext/stock
- Marley pharmacy linkage: https://marley.health
