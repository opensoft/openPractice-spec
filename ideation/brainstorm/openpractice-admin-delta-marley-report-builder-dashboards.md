Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Delta A7 — Report Builder & Dashboards

## What the capability is

Non-developer reporting: query reports and report view over any
documents, named filters, charts and dashboard views, exports (CSV/
Excel), and scheduled email delivery — all over the same permissioned
data model.

## How the leader implements it

Frappe's report engine works off DocTypes natively; every list view
can be filtered, grouped, charted, and exported; scripted reports and
custom print formats extend it when SQL/code is needed. Marley's
entities participate automatically.

## Where the other system stands

OpenEMR has a solid built-in reporting set and a Generic Engine
(forms-based reporting), but ad-hoc cross-entity reporting typically
requires SQL or developer help — the self-serve surface is narrower.

## Implication for openPractice

Operator-visible value: practice managers answer their own questions
(AR aging, visit volumes, supply burn) without tickets. Combined with
A2/A6 this is the showcase trio for the Frappe stack. Watch the flip
side: naive report-building on transaction tables at scale needs caching
or warehouse guidance once tenants get large.

## Build / adopt options

- Adopt; curate a starter dashboard set per role in the reference
  install.
- Add reporting-scale guidance to the ops docs before large tenants land.

## Verification links

- Frappe reports/dashboards: https://frappeframework.com/docs
