# Purpose-Bound Business Views — Brainstorm

Status: brainstorm
Kind: architecture
Summary: useful named customer financial views and cross-domain revenue analysis should use purpose-limited projections with independent controls for access, exports, and outreach.
Topics: openpractice-customer-financial-separation, reporting, patient-engagement, platform-security
Repository context: openPractice — business reporting and communication across connected financial and customer systems
Captured: 2026-11-14

## Possible feats

- **Named financial view** — show an authorized user customer, membership and transaction facts without chart access.
- **Cross-domain revenue projection** — distinguish subscriptions, direct-pay care, insurance revenue and retail with reproducible metric definitions.
- **Purpose-aware output contract** — authorize report delivery and campaign use separately from the ability to compute an audience.

## Focus

How can the business use its customer and revenue information without a broad
chart replica or an unrestricted marketing customer database?

## Proposed model

Classify each projection by source, recipient, purpose, allowed fields,
relationship/tenant scope, sensitivity, retention obligations and output channel.
Use a few shipped role recipes and destination profiles, consistent with the
existing Roster design, rather than exposing a new policy editor.

| View | Useful contents | Default boundary |
|---|---|---|
| Customer financial record | Necessary identity/contact, membership terms, invoices, payments, credits, refunds, balance | Protected when it reveals care or healthcare payment |
| Business performance | Offering/category, campaign attribution, tenure, retention, gross/net revenue, refunds, fees, margin inputs | Authorized joins; chart notes and diagnoses excluded |
| Wider management report | Purpose-appropriate aggregates and reconciliation status | No claim of de-identification without an applicable method and review |
| Outreach work | Approved audience, recipient/channel authority, approved message and required permissions | A report audience alone grants no send authority |

The source of truth for a metric is its versioned definition plus authoritative
source events. State whether a measure is invoiced, collected, recognized or
net of refunds/fees. Separate campaign-attributed revenue from demonstrated
incremental lift. Suppress unauthorized drill-downs and exports even when the
aggregate is visible. A recognizable small cohort is not automatically anonymous.

## Accounting and vendor access

Assume accounting has customer identities. Keep necessary billing contact,
membership/invoice/payment facts and controlled source references. Default
exclusions include chart notes, diagnoses, medications, test results, full claim
payloads and medical record numbers. An exception requires a documented purpose,
recipient, fields and authority; useful business data is not removed merely to
label a system PHI-free.

The accountant's access is scoped to the financial job; the outsourced biller's
access is scoped to reimbursement work. They can be different actors at the
same firm. Neither role implies general chart access. Apply actor attribution,
time limits, revocation and the applicable vendor/BAA checks to connected systems
and exports, including attachments and support bundles, not only structured fields.
No vendor is selected or declared suitable by this packet.
[HHS business associate guidance](https://www.hhs.gov/hipaa/for-professionals/privacy/guidance/business-associates/index.html)

## Outreach is a separate use

Business analysis and sending an offer are different operations. Evaluate the
actual purpose, audience origin, recipient authority, communication preferences,
applicable authorization or exception, vendor scope and current revocation at
send time. Generic email opt-in is not automatically a HIPAA marketing
authorization. Communications about the practice's own health-related services
may fit an exception; unrelated or third-party promotions need their own review.
Unresolved authority holds the campaign, not access to care.
[Current marketing definition](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.501)

An independently acquired retail contact need not become a patient record.
Moving patient data into a retail system does not erase its origin or the
conditions on its use. A formal hybrid-entity structure, if considered, is
separate organizational/legal work, not a property of this software diagram.

## Interfaces and boundaries

Receives selected, attributed events from medical billing, commerce,
subscriptions and accounting. Emits purpose-specific views, reproducible
metrics, authorized export packages and communication intents through existing
contracts. It does not copy the chart, authorize treatment, create obligations,
move money, or turn a pseudonymous customer key into de-identified data.

## Alternatives and tensions

A customer-wide view improves service but can cross payer, family or tenant
boundaries. Aggregate-only reporting loses operational usefulness. Controlled
named views and separately reviewed broader outputs preserve both needs.
Applicable HIPAA and other jurisdictional duties remain deployment-specific.

## Open questions

- Which named views and outreach purposes are needed by the first practice?
- Which reports can use aggregates and which require identifiable financial detail?
- What approved profiles govern recipient access, retention and vendor contracts?

## Relationships

Extends [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md),
[Patient Engagement And Communications](openpractice-feature-list-patient-engagement-and-communications.md),
and [Roster Access](openpractice-small-practice-beta-roster-access-and-offboarding.md).
Combined with [Identity](openpractice-customer-financial-separation-identity.md)
and [Financial Routing](openpractice-customer-financial-separation-financial-routing.md)
in [Customer To Books](openpractice-customer-financial-separation-synthesis-customer-to-books.md).
