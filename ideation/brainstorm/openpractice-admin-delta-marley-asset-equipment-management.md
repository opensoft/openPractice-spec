Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Delta A5 — Asset & Equipment Management

## What the capability is

Lifecycle management of capital equipment: asset registers, purchase
to capitalization, maintenance schedules and logs, depreciation, and
disposal — the "when was the autoclave last serviced" class of
operational questions.

## How the leader implements it

ERPNext's Asset module, available to any facility running Marley,
covers the full lifecycle including scheduled maintenance (Preventive
Maintenance module) and accounting depreciation entries.

## Where the other system stands

OpenEMR has nothing in this space; practices track equipment in
spreadsheets or separate CMMS software.

## Implication for openPractice

Inherited-if-adopted. Relevance scales with facility scope: outpatient
clinics own a handful of assets; labs and imaging centers are
asset-heavy and will treat this as real value. Regulation-adjacent:
maintenance logs on patient-care devices are part of healthcare
facility compliance in most jurisdictions, including for labs.

## Build / adopt options

- Adopt: include ERPNext Asset + Maintenance in the deployment
  profile, off by default for outpatient installs.
- No custom build needed; document the operating pattern.

## Verification links

- ERPNext Asset: https://docs.frappe.io/erpnext/asset
