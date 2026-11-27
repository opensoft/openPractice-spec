Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Delta A9 — US Claims Pipeline & ONC Certification (OpenEMR's Lead)

## What the capability is

The full US commercial insurance revenue cycle: ANSI X12 EDI (837
claims out, 835 remittance in), clearinghouse integration, eligibility
checks, UB-04 institutional forms, ERA/EOB posting, and — the umbrella
— ONC Health IT certification that makes the stack legal in many
payer-required workflows.

## How the leader implements it

OpenEMR has decades of payer-facing maturity: certified through ONC's
criteria, X12 generation/ingestion built in, direct integrations to
major clearinghouses, institutional billing support, and mature
remittance posting with responsibility-split until zero balance.

## Where the other system stands

Marley's billing stops at invoicing. There is no X12, no clearinghouse
integration as shipped, and no ONC certification — the Frappe
healthcare ecosystem has not completed a US claims stack. Anything in
this direction is a build.

## Implication for openPractice

This is the one administrative delta where OpenEMR is genuinely ahead
and where the gap is expensive. Two strategic readings:

1. If openPractice's first market is Puerto Rico/private-pay (cash,
   card, membership), defer entirely; invoicing (A8) suffices.
2. If US insurance billing is ever in scope, this is a top-tier build:
   either partner with a clearinghouse API vendor (Stedi, Waystar-like)
   and build EDI plumbing on Frappe, or accept a long certification
   journey. Certification also bleeds back into openChart (C-CDA,
   e-prescribing clinical criteria), so it is not contained here.

Milestone gating: do not commit openPractice to US claims until a
target-payer pressure exists — it is months of work alone.

## Build / adopt options

- Defer (recommended default): invoice-only v1.
- Partner: clearinghouse API integration as the claims path of record.
- Buy-not-build certification only if payer demand forces it.

## Verification links

- ONC certification listing for OpenEMR: https://open-emr.org/wiki/certification
