## ADDED Requirements

### Requirement: SC-01 One catalog item per service the practice books or charges
openPractice SHALL keep a service catalog with one item for each service the practice books or charges. Each item SHALL carry:
- an identifier and an administrative name that describes the service for staff and statements, never a clinical description;
- the billing code references it uses, each as a code system and a code only (SC-02);
- default units (the place of service never comes from the item; CG-01 states where it comes from);
- whether it can be booked, and if so its usual duration;
- its service category (SC-05) and its offering-route facts (SC-06);
- its effective dates and status.

A charge line SHALL reference exactly one catalog item (CG-01). An item SHALL never be deleted once a charge, estimate or appointment has used it; it SHALL be ended with an end date.

#### Scenario: An item is retired
- **WHEN** the practice stops offering a service that past charges used
- **THEN** the item is given an end date, it can no longer be booked or charged after that date, and past charges still show it

### Requirement: SC-02 Code sets ship license-free, and CPT comes from the practice's own licensed overlay
The product SHALL ship only license-free code content: ICD-10, HCPCS Level II, place-of-service codes and the payer adjustment codes (FD-11). CPT codes SHALL come only from an overlay the practice imports from its own licensed source, and the repository SHALL never contain CPT descriptors or other licensed text. Every code reference SHALL carry a code system and a code, never descriptor text (FD-11, EB-13). A catalog item that references a CPT code SHALL NOT be activated until the practice's overlay is imported and covers that code. The import SHALL record the practice's attestation that it holds the right to use the content. Whether a practice may import its own CPT content into a self-hosted product is a question for counsel (risk R-1): the import is designed now, and counsel's recorded answer SHALL be a go-live condition for activating any CPT-coded item in production, on the pattern of ruling 102 (design call CR-19, PROPOSED).

#### Scenario: A CPT-coded item before the overlay
- **WHEN** an administrator activates a catalog item that references a CPT code the practice has not imported
- **THEN** activation is refused, and the missing overlay is named

### Requirement: SC-03 One practice price list, read by date of service
openPractice SHALL keep one price list for the practice, with one effective-dated price per catalog item (ruling 126). The price of a charge, an estimate or a Good Faith Estimate SHALL be the price in force on the date of service. A price change SHALL take effect from a stated date and SHALL NOT change any charge, estimate or Good Faith Estimate already made. An estimate or Good Faith Estimate whose date of service falls after a price change made since it was issued SHALL raise a work item before the patient is billed (design call CR-20, PROPOSED).

#### Scenario: A price rises mid-month
- **WHEN** an office visit's price rises from 150.00 to 160.00 from the 15th, and a visit on the 10th is charged on the 16th
- **THEN** the charge is priced at 150.00

#### Scenario: A Good Faith Estimate goes stale
- **WHEN** a Good Faith Estimate was issued at 150.00 for a visit on the 20th, and the price rises from the 15th
- **THEN** a work item is raised before the patient is billed, and the issued estimate is unchanged

### Requirement: SC-04 One self-pay discount, stated once
openPractice SHALL keep one self-pay discount for the practice: a single percentage off the price list, effective-dated (ruling 126), set by a holder of the practice manager recipe (`define-actors-agents-and-work-inbox` AM-02), who is recorded as the person who set it, because the practice manager role holds the powers a rule gives "the owner" (ruling 193; design call F-13 of `define-mvp-practice-foundation`, ruled by it; design call CR-21, PROPOSED). It SHALL apply when a charge is finalized for a patient with no coverage for that service, and SHALL post as `self_pay_discount` (RT-06). It SHALL NOT apply automatically to a patient who has coverage from a federal health program and chooses to pay for the service themselves; such a case SHALL need a recorded decision for that visit by a holder of the practice manager recipe (design call CR-21, PROPOSED), and whether the discount may be offered at all in that case is a question for counsel. The discount SHALL be rounded once per charge line, half away from zero, to the cent (BAB-18). No power here, or anywhere in this change, SHALL come from being recorded as an owner of the practice under PR-15's owner links: a recorded owner grants nothing by itself and is used only to check independence (ruling 193).

#### Scenario: An uninsured visit
- **WHEN** a patient with no coverage has a 200.00 visit and the self-pay discount is 30 percent
- **THEN** the charge posts 200.00 as `service_self_pay` and 60.00 as `self_pay_discount`, so the patient owes 140.00

#### Scenario: A Medicare beneficiary chooses to pay
- **WHEN** a patient with Medicare coverage asks to pay for a service themselves
- **THEN** the discount is not applied unless a holder of the practice manager recipe records a decision for that visit

### Requirement: SC-05 Each catalog item maps to one shipped service category
Each catalog item SHALL map to exactly one service category from the shipped list (RT-03). The mapping SHALL be versioned and effective-dated, and a change SHALL apply only from its effective date (RT-03).

#### Scenario: An item with no category
- **WHEN** an administrator tries to activate a catalog item with no service category
- **THEN** activation is refused

### Requirement: SC-06 Each catalog item carries its offering-route facts
Each catalog item SHALL carry the offering-route facts that `define-customer-financial-data-boundaries` requires for every financial offering ("Offerings determine financial routing at line level"): its billing route (insurance-billed healthcare, direct-pay healthcare, membership or retail), its obligation owner, its selling entity, its currency, its accounting mapping and its disclosure profile. This change SHALL charge only items routed as insurance-billed or direct-pay healthcare; membership and retail obligations belong to the routes of that change (rulings 24 and 26). Charge finalization SHALL check the route and the patient's membership entitlement before any invoice or claim (BAB-16, CG-05). An item whose route is unresolved SHALL hold finalization as work (BAB-16).

#### Scenario: A retail item in the catalog
- **WHEN** a catalog item is routed as retail
- **THEN** this change creates no charge line for it, and the item's sale follows the retail route of the customer-financial change

### Requirement: SC-07 No payer fee schedules are loaded
openPractice SHALL NOT load payer fee schedules or contracted rates in this change (ruling 126). What the practice expects a payer to pay SHALL come from the revenue estimator (RE-03). Importing a payer's contract to dispute its payments (FD-2) SHALL need a later governed change.

#### Scenario: Someone tries to load a payer's fee schedule
- **WHEN** an administrator tries to import a commercial payer's fee schedule
- **THEN** the system offers no such import

### Requirement: SC-08 Catalog and price changes are versioned, audited and do-and-notify
Every change to a catalog item, a price, the self-pay discount or a category mapping SHALL create a new effective-dated version, keep the earlier versions retrievable, and be audited with its actor and time. Each SHALL be FD-5 tier 2: done at once, with a notification (design call CR-86, PROPOSED). A change to the self-pay discount SHALL be made by a holder of the practice manager recipe (SC-04; ruling 193). An agent SHALL stay at `propose` for these changes (TH-13).

#### Scenario: The discount changes
- **WHEN** a holder of the practice manager recipe changes the self-pay discount from 30 to 25 percent from the first of next month
- **THEN** a new version takes effect on that date, staff are notified, and charges before it keep 30 percent
