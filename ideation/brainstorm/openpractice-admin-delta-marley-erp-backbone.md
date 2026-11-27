Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Delta A1 — Integrated ERP Backbone (Accounting, Buying, Stock)

## What the capability is

A single database in which clinical operations coexist with thegeneral ledger, accounts receivable/payable, purchasing, and warehouse
stock. An invoice raised at a consultation posts to accounting without
integration plumbing; a purchase order for supplies lands in the same
books as the patient charge it eventually supports.

## How the leader implements it

Marley Health runs its healthcare domain as a Frappe app beside
ERPNext, so GL, AR/AP, buying, and stock are ERPNext's own modules —
no embedded subset, the real thing. Shared masters (Company, Item,
Customer) are reused by the healthcare flows rather than duplicated.

## Where the other system stands

OpenEMR has fee sheets, payment posting, and reporting, but no general
ledger, no procurement, no warehouse inventory. Practices exporting to
QuickBooks or Xero is the norm; integration is user-built.

## Implication for openPractice

If openPractice is Frappe-native, this delta is inherited, not built.
The work is boundary design, not implementation: which entities are
clinical (patient) vs commercial (customer), and how identity is
shared. That boundary already has its own packet —
openpractice-customer-financial-separation — and this delta
effectively is that packet's enabling condition.

## Build / adopt options

- Adopt: run openPractice as a Frappe app adjacent to ERPNext (status quo).
- Defer: nothing to build; document which ERPNext modules are in scope for v1.
- Watch: ERPNext submodule surface is large — constrain defaults or
  implementers will drown in configuration.

## Verification links

- ERPNext modules: https://docs.frappe.io/erpnext
- Marley repo: https://github.com/earthians/marley
