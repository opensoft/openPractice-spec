# Customer Identity And Patient Roles — Brainstorm

Status: brainstorm
Kind: architecture
Summary: one person may have customer, patient, member, payer, and representative roles while each system receives only the identity and relationship information its work requires.
Topics: openpractice-customer-financial-separation, registration, patient-billing, platform-security
Repository context: openPractice — administrative identity shared with connected EHR, commerce, and accounting systems
Captured: 2026-11-14

## Possible feats

- **Role-aware customer identity** — link customer and patient records without requiring every customer to become a patient.
- **Protected identity crosswalk** — correlate domain identifiers without distributing all relationships to every recipient.
- **Purpose-specific contact projection** — carry billing and delivery contacts while preserving confidential-contact instructions and representative authority.

## Focus

How can the practice recognize one person across care, memberships, purchases,
and payments without giving every user or application the person's full record?

## Captured direction

The owner asked to assume customer identity is present in accounting and to
remove unnecessary patient information from it. This establishes a limited,
protected customer and financial record as the accounting design assumption.
It does not establish that every commerce customer is a patient or that names
alone are always PHI.

## Proposed model

Extend the existing plain party register rather than introducing a competing
identity spine. Separate the person from effective-dated roles:

| Role | Meaning | Domain record |
|---|---|---|
| Customer | Person or organization purchasing an offering | CRM/commerce and accounting customer |
| Patient | Individual receiving healthcare | Administrative registration linked to the EHR patient |
| Member | Subscriber or beneficiary of a membership | Subscription agreement and covered-person links |
| Payer / guarantor | Party responsible for a particular obligation | Financial relationship scoped to that obligation |
| Representative | Party with a defined authority to act or receive information | Relationship with scope, evidence, and effective dates |

A member, patient, purchaser, and payer can be different parties. A membership
subscriber is not automatically the insurance subscriber. An employer paying
an invoice does not thereby receive an employee's care or purchase history.

Maintain a controlled crosswalk among the operational party, EHR patient,
commerce customer, subscription, and accounting customer identifiers. Matching
uses verified links and provenance; shared email, phone, address, or payment
method alone does not authorize a merge or a household disclosure. Corrections
preserve prior mappings and the domain records' financial history.

This is a logical identity model. It does not require a new master-data product,
a universal public identifier, or automatic synchronization of every contact.

## Interfaces and boundaries

| Recipient | Candidate identity fields, subject to purpose and authority | Excluded by default |
|---|---|---|
| Accounting | Customer identifier, name, billing address, necessary billing contact and authorized payer link | Chart, diagnosis, medications, test results, insurance member numbers, EHR medical-record number |
| Subscription / commerce | Customer identity, membership beneficiary, necessary delivery/contact details, preferences | Full chart or a copied insurance claim |
| Medical billing | Required patient, subscriber, guarantor, coverage and service identities | Unrelated retail purchase history |
| EHR | Verified patient linkage and necessary registration updates | A complete customer sales or campaign history |

One owning source and update direction must be specified for each shared field.
Updates to name, billing address, and confidential communication destination are
separate changes; an accounting address correction must not overwrite an EHR's
clinical identity or a confidential-contact instruction. Reject or queue ambiguous
matches instead of propagating them.

Names and contact details linked to care or healthcare payment can be PHI.
Treat the crosswalk and patient-linked customer records accordingly. A generic
identifier or a separate customer table is not a de-identification method.
[HHS PHI and de-identification guidance](https://www.hhs.gov/hipaa/for-professionals/special-topics/de-identification/index.html)

The reference pattern is identity plus role-specific records. FHIR's
[Person resource](https://www.hl7.org/fhir/R5/person.html) demonstrates that
pattern and warns that links can reveal sensitive relationships. Customer and
membership roles here are proposed business extensions; this reference does not
change the existing FHIR R4 interoperability target or require FHIR Person support.

## Alternatives and tensions

A single unrestricted customer/patient record simplifies joins but broadens
access. Fully disconnected directories create duplicate entry and reconciliation
errors. Controlled projections preserve useful linkage with distinct permissions.
The linked identity itself remains sensitive even when clinical fields are absent.

## Open questions

- Which existing application owns each demographic and contact field in the first deployment?
- Which household and organization-paid membership arrangements are required first?
- Which accounting customer fields are genuinely required by the selected connector?

## Relationships

Extends [Registration And Demographics](openpractice-feature-list-registration-and-demographics.md)
and the party substrate in [Feature Normalization](openpractice-small-practice-beta-feature-normalization.md).
Combined with [Financial Routing](openpractice-customer-financial-separation-financial-routing.md)
and [Purpose-Bound Business Views](openpractice-customer-financial-separation-business-views.md)
in [Synthesis: Customer To Books](openpractice-customer-financial-separation-synthesis-customer-to-books.md).
