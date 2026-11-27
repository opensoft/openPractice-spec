Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Delta A2 — Metadata-Driven DocType Framework

## What the capability is

Every business object (entity definition, fields, validations,
permissions, list/form UI, REST endpoints, versioning surface) is
declared as a DocType in metadata. New objects, custom fields,
workflows, and print formats are created through the framework and
migrate as files — developers and power users extend the product
without editing core code.

## How the leader implements it

Frappe's app architecture is DocType-native; Marley's healthcare
entities (Patient, Encounter, Therapy Plan, etc.) are ordinary
DocTypes that buyers can extend. Custom DocTypes sit in a custom app
and survive upgrades.

## Where the other system stands

OpenEMR offers LBF (layout-based forms) and CAMOS templates for
encounter forms, plus a module/plugin path (LAMM modules). Real
extension of core entities requires PHP changes and schema migrations
— a much higher bar and a fork-risk path.

## Implication for openPractice

This is the single strongest technical argument for the Frappe stack:
your implementer ecosystem can do field-level and entity-level
customization per client without forking openPractice. It also means
openPractice's own internal discipline matters — everything new should
be a DocType in your app, not ad-hoc schema.

## Build / adopt options

- Adopt as-is; publish a customization guide for implementers early.
- Define extension points (hooks, app boundaries) so client apps do
  not drift into core modifications.

## Verification links

- Frappe DocTypes: https://frappeframework.com/docs
- OpenEMR form customization: https://open-emr.org/wiki (LBF / forms)
