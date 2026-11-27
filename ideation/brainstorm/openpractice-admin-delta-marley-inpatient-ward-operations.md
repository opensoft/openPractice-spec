Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Delta A3 — Inpatient & Ward Operations

## What the capability is

Facility-aware operations: service units (departments, wards, rooms,
beds) as schedulable resources, patient admission–discharge–transfer
tracking, bed occupancy as a first-class fact, and occupancy-linked
billing.

## How the leader implements it

Marley models Healthcare Service Units as a tree (facility → ward →
room → bed), schedules practitioners against units, tracks inpatient
occupancy, and can generate bed charges from occupancy records.
Encounter types distinguish outpatient, inpatient, and other visit
forms.

## Where the other system stands

OpenEMR is outpatient-first. It can schedule providers and rooms as
calendar categories, but has no bed occupancy model, no ADT feeds, and
no occupancy billing. Hospital usage of OpenEMR typically pairs it
with other systems or customizations.

## Implication for openPractice

Facility scope is a product decision, not a feature gap: if v1 is
outpatient-only, this delta is Marley-for-free later; if v1 includes
clinics-with-beds or overnight observation, service units and ADT are
day-one scope. Either way the shape already exists in Marley to study
— borrowing its data model is cheaper than greenfield design.

## Build / adopt options

- Defer (outpatient v1): design Patient Location as an extension-safe
  optional module.
- Adopt (facility v1): port Marley's Healthcare Service Unit model
  and occupancy billing rules.

## Verification links

- Marley repo docs (service units, inpatient): https://marley.health
