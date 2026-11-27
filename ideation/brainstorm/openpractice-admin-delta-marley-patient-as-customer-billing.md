Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Delta A8 — Patient-as-Customer Revenue Flow

## What the capability is

The commercial identity of the encounter: how a patient produces an
invoice, receipts post against it, statements issue, and AR ages — and
whose name is actually on the invoice (patient, guardian, employer,
insurer's residual balance after adjudication).

## How the leader implements it

Marley/ERPNext treat billing through the generic Customer machinery:
encounters generate sales invoices against the relevant customer
identity, payments post through payment entries, and AR reporting is
the standard ERPNext engine. Simple, but customer-identity-centric.

## Where the other system stands

OpenEMR models fee sheets, encounters' charges, insurer and patient
responsibility splits natively — because US billing demands it — but
that model is calcified into the claims pipeline (see A9); its
non-claims invoicing is no more sophisticated.

## Implication for openPractice

This delta intersects directly with the existing
openpractice-customer-financial-separation packet: patient identity in
clinical context vs customer identity in financial context, with
minimization of unnecessary patient data in the financial domain.
Marley's approach demonstrates the end-state (identity shared across
domains, one ledger); openPractice's contribution is the disciplined
boundary around it. The payer-of-record distinction (patient vs
guarantor vs insurer) is the key entity design point.

## Build / adopt options

- Adopt ERPNext invoicing as the v1 revenue engine for private-pay markets.
- Design the Guarantor/Payer link now; it is much costlier to retrofit.
- Defer insurer contracts to whatever A9 resolves to.

## Verification links

- openpractice-customer-financial-separation packet, same directory.
