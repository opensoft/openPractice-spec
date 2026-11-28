Status: draft

# Design: data the onboard must collect

This is the data dictionary for practice onboarding. Every field is typed, its verifier is named where one exists, and its gate is marked: **intake** (required for `intake-complete`) or **before-billing** (required for `billing-ready`). The split is open decision O-1 below; the marks here are the proposed default.

## 1. Practice record (KYB) - capability `practice-register`

| Field | Type | Verified by | Gate |
|---|---|---|---|
| Legal entity name | string | SOS state filings match display | intake |
| Doing-business-as | string[] | none | intake |
| Federal EIN | `XX-XXXXXXX` | IRS TIN match or W-9 attestation | intake |
| Entity type | enum (LLC, PLLC, PC, Corp, Sole prop, Nonprofit, Other) | W-9 box | intake |
| State and date of formation | code, date | SOS | intake |
| Signed W-9 | document ref | embedded, checksum | intake |
| Owner/controller list | controlling individual refs | see 1.1 | before-billing |
| Primary admin contact | name, email, phone | email verification | intake |
| Agreements | openPractice terms version, BAA identifier, state addenda | countersign record | intake |
| Lifecycle state | enum | governed transitions only | both |

### 1.1 Controlling individuals (Medicare-contingent)

For every owner >= 5% and every managing employee/board member (CMS ownership disclosure set used by CMS-855/PECOS): legal name, DOB, **SSN**, percentage owned, role/title. SSNs are stored only as vault references (open decision O-2); the register holds name, role and percentage.

Gate: **before-billing**, and required only when any billing entity of the practice targets Medicare.

## 2. Billing entity record - capability `billing-entity-register`

One per Tax ID that bills. A practice has at least one; an MSO arrangement may add the MSO's own entity alongside the clinic's.

| Field | Type | Verified by | Gate |
|---|---|---|---|
| Display name | string | none | intake |
| Tax ID reference | ref to practice EIN or distinct EIN | IRS match | before-billing |
| NPI type 2 (organizational) | 10-digit NPI | NPPES lookup | before-billing |
| Taxonomy codes | primary + additional, NUCC format | NUCC registry | before-billing |
| Pay-to address | address | USPS standardization | before-billing |
| Mail-to / correspondence address | address | USPS | intake |
| Medicaid provider ID | string + state | state portal status | before-billing |
| Medicare group PTAN (if linked) | string | MAC enrollment response | before-billing |
| Bank account | reference to `billing-accounting-boundary` bank register (encrypted) | register verification | before-billing |
| Fee schedule reference | versioned rate card ref | release approval | before-billing |
| Clearinghouse route per payer | route decision record, see 2.1 | participation list + cost stamp | before-billing |
| Lifecycle state | enum | governed transitions | both |

### 2.1 Clearinghouse route decision record

Per (billing entity x payer): route (`stedi` | `officeally`), decision basis (participating-payer list check with list version and check timestamp; expected per-transaction vs flat cost at projected volume), effective date, and supersedes pointer. The default rule of the record: Medicare-only volume routes to Office Ally; any commercial non-participating volume routes to Stedi. The record keeps the rationale so the route can be re-litigated when payer participation or volume changes.

## 3. Service location record - capability `service-location-register`

| Field | Type | Verified by | Gate |
|---|---|---|---|
| Site name | string | none | intake |
| Physical address | address | USPS | intake |
| Place-of-service code | POS code set | CMS POS list | before-billing |
| Phone, fax | strings | none | intake |
| Operating hours | structured | none | intake |
| Site licenses | pharmacy license (state, #, expiry), CLIA # (+waiver level) if lab, accreditation body/ID if any | issuing authority or document image | before-billing |
| Linked billing entity | ref | existence | intake |
| Active flag | bool | tie to `billing-ready` of entity | both |

## 4. Provider roster record - capability `provider-roster`

Per provider rendering or supervising at the practice.

| Field | Type | Verified by | Gate |
|---|---|---|---|
| Full legal name, degree | string | diploma/license | intake |
| NPI type 1 | 10-digit | NPPES | intake |
| Taxonomy | NUCC | NPPES | intake |
| CAQH ID + last attestation date | string, date | CAQH ProView export | before-billing |
| State license(s) | state, #, issue/expiry | primary source (state board portal) | before-billing |
| DEA registration # | string (if prescriber) | DEA lookup; **DEA numbers vault-referenced** | before-billing |
| Board certifications | specialty board, #, expiry | ABMS/board export | before-billing |
| Malpractice policy | carrier, policy #, limits, expiry; face sheet document | document + carrier | before-billing |
| Medicare PTAN link to billing entity | string | MAC enrollment response | before-billing |
| Medicaid provider ID | string + state | portal | before-billing |
| Supervising/collaborative relationships | provider refs, agreement doc refs | countersigned doc | before-billing |
| Document packet | CV, licenses, DEA, board, face sheet, W-9 per provider | document store w/ checksum | before-billing |
| Expiry alerts | computed | alerts at 90/60/30 days pre-expiry | continuous |

Credential documents are assigned to a retention record family per `add-reimbursement-retention-control`; documents of records created in error with no linked activity follow the same exclusion as patient records (ruling 9).

## 5. Enrollment matrix - capability `enrollment-tracker`

State of every enrollment, keyed by (billing entity or provider) x payer x transaction type.

- Transaction types: eligibility 270/271; claims 837P/I/D; remit 835; status 276/277.
- Status enum: `not-started`, `docs-collected`, `submitted`, `payer-pending`, `approved`, `rejected`, `suspended`.
- Fields: status + timestamps per transition, payer enrollment ID/reference, effective date, path (`stedi-api` | `officeally-sftp` | `manual-portal`), evidence link (response document or portal screenshot ref), single named resolver per open enrollment, re-enrollment event trail.
- Programmatic path: Stedi Enrollments API creates and tracks these where Stedi supports the payer; everything else degrades to `officeally-sftp` or `manual-portal` with the same records.

## 6. Readiness gates

- `intake-complete`: all intake-gate fields valid, agreements countersigned, NPPES checks passed. Unlocks scheduling and registration slices for the practice.
- `billing-ready` (per billing entity): all before-billing fields + bank registered + fee schedule approved + for at least one payer the enrollment trio (270/271, 837, 835) is `approved`. Charge finalization for a payer stays blocked until that payer's trio is green; FD-5 tier-3 escape applies at the filing threshold, as in `define-mvp-practice-foundation`.

## Open decisions

- **O-1 Intake vs before-billing split.** The marks above are the proposal: identity and NPI at intake; EIN-adjacent IRS, ownership, banking and enrollments before billing. Owner rules before closure.
- **O-2 Vaulting.** SSNs, DEA numbers and banking records live in a vaulted sub-system with reference pointers, extending the BAA-gate precedent of `define-mvp-practice-foundation` (AI gate). Whether vaulting is built or bought is out of scope here.
- **O-3 Practice vs MSO tenancy.** Whether the register treats an MSO and its clinics as one practice with many entities or several practices under a shared owner key is left to the platform tenancy decision; either satisfices this change's data model.
