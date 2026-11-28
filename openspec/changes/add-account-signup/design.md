Status: draft

# Design: signup vs onboarding, and the account substrate

## 0. The split, stated once

| | Signup (this change) | Onboarding (`add-practice-onboarding`) |
|---|---|---|
| Question answered | Who can log in, who pays the SaaS bill | Is this a legitimate healthcare business, and can it bill payers |
| Audience | A person | The business |
| Duration | Minutes, self-serve | Days to weeks (payer-driven) |
| Verification | Email, MFA | NPPES, IRS TIN, CAQH, primary-source licenses |
| Output | Activated account in an empty workspace | Practice record at `billing-ready` |
| Friction doctrine | Minimum — every field costs signups | Depth is expected — the customer already committed |

Signup SHALL collect no field already collected by onboarding; the only crossover is that the sign-up email pre-populates the practice's primary admin contact if the user allows it.

## 1. Account record - capability `account-register`

| Field | Type | Verified by | Notes |
|---|---|---|---|
| Account key | opaque id | system | join key everywhere |
| Owner identity | person record: name, date of birth optional | none at signup | a person, not the business |
| Email | string, unique | email verification link/code | required before any workspace use |
| Credential | password hashed by documented KDF, or SSO reference (OIDC/SAML issuer + subject) | KDF parameters versioned | passwords stored only as hashes; SSO for enterprise/MSO |
| MFA state | enum (none, totp, passkey), method refs | enrollment ceremony | `totp` or `passkey` REQUIRED before any workspace can reach non-synthetic data |
| Plan | versioned plan ref | countersign/checkout record | SaaS billing of the subscription itself is owned by the finance function, not this change |
| Invitations | list: email, role, expiry, inviter ref | email verification on accept | see 4 |
| Lifecycle | `active`, `suspended`, `closed` + transitions | governed, FD-5 tiered per §7 | transitions tiered after FD-5 mapping reconciliation |

Signup form minimum: name, email, password or SSO, plan choice. Nothing else. Workspace display name defaults from email domain or practice guess and is editable.

## 2. Workspace - capability `workspace`

| Field | Type | Notes |
|---|---|---|
| Workspace key | opaque id | tenant boundary; this is the tenant `define-mvp-practice-foundation` assumes but never defines |
| Region | enum from approved regions | data residency anchor |
| Tenancy shape | `single-practice` or `multi-practice (MSO)` | decision D-2; frozen once the first practice record exists |
| Data mode | `synthetic` until any linked practice is `intake-complete` | enforcement point for the repo's synthetic-only convention |
| Created by / created at | refs | audit |

Everything created inside a workspace — patients, schedules, claims — belongs to that workspace; cross-workspace access is never implicit and, for patient data, requires the governed cross-reference mechanisms of `define-mvp-practice-foundation`, not this change.

## 3. Account-practice link - capability `account-practice-link`

- **Create flow (self-serve):** account creates workspace at signup; the onboarding wizard starts a `prospect` practice record inside the workspace; the registrar relationship is the link.
- **Claim flow (white-glove):** staff-created practices (MSO onboardings run by openPractice operations) are claimed by the owner account via signed invitation; claim acceptance carries a person confirmation, mirroring the `link_existing` precedent (tier-2) in `define-mvp-practice-foundation`.
- **Shape:** exactly one owning account per practice; zero or more member accounts with roles (the role catalog itself is owned by `define-actors-agents-and-work-inbox`, not this change).
- **Revocation:** audited, tiered per §7, with the work-inbox handoff so no human is stranded mid-task.

## 4. Users are not providers (and accounts are not actors)

The repo now names four people-populations:

| Population | Owning change | The person in that frame |
|---|---|---|
| Account (user) | this change | someone who authenticates |
| Actor / agent | `define-actors-agents-and-work-inbox` | someone who performs governed work |
| Provider | `add-practice-onboarding` (`provider-roster`) | someone who renders or supervises care |
| Staff | `add-staff-scheduling-and-hr-integrations` | someone who occupies a schedule slot |

- A user may hold no clinical credential (front desk) and may never be an actor record beyond authentication.
- A credentialed provider may never log in (locum, referring provider) and never appear in Shifts.
- A given person MAY be several of these at once; the representation is always cross-references between records, never merged tables.
- Functions that need the authentication interpretation use the account; governance interpretation uses the actor; clinical interpretation uses the provider; scheduling interpretation uses staff.

## 5. Gate chain

| State | Unlocked | Blocked |
|---|---|---|
| Signed up, email not yet verified | nothing except verification | everything |
| Email verified | synthetic demo workspace, plan management, invitations, starting the onboarding wizard | any non-synthetic record |
| Practice `intake-complete` | real patient records, live scheduling and patient gateway | charge finalization, claims |
| Billing entity `billing-ready` | charge finalization and claims per green enrollment trio (owned by `add-practice-onboarding`) | payers whose trio is not green |

The BAA placement: clickwrap terms at signup; **countersigned BAA is an intake-gate requirement** of `add-practice-onboarding`. This change enforces the consequence — non-synthetic data entry is impossible before the intake gate — rather than the signature itself.

## 6. Fraud and abuse notes

- Email verification and the synthetic-only mode are the abuse boundary: junk signups can never see or create real data.
- Workspace creation rate limits and invitation expiry are operational defaults, tunable per deployment.
- Full account close keeps retention families from `add-reimbursement-retention-control`; clinical and financial records of a linked practice follow their own families, not the account's fate.

## 7. FD-5 tier reconciliation (proposed, pending owner ruling)

FD-5's principle is that reversible, delayed actions sit below the explicit sign-off tiers, while identity and release actions carry confirmations (`define-mvp-practice-foundation` lists: `link_existing` at tier 2, create-new and revocation requests, identity-review resolutions, export-batch release). Proposed mapping here:

- Account suspension: reversible and delayed — proposal leaves it to the FD-5 handling of reversible actions, **not** an explicit tier; open for ruling. Account suspension of the owning account of a billing-ready practice is proposed tier-3 because it strands revenue operations.
- Practice claim (white-glove): tier-2, by direct analogy to `link_existing`.
- Owner change on an account: tier-3 (identity-bearing, hard to reverse cleanly).
- Account close: tier-3 with retention handoff.
- Workspace tenancy-shape freeze: automatic at first practice record; no sign-off, recorded in audit.

## Open decisions

- **D-1 BAA timing.** Proposal: terms clickwrap at signup; countersigned BAA at the intake gate. The alternative (moved fully to signup) would flip the synthetic-only enforcement to signup time. Owner rules.
- **D-2 Tenancy shape (absorbs O-3 of `add-practice-onboarding`).** Proposal: `multi-practice` available at workspace creation, frozen once the first practice record exists; MSO accounts use one workspace with N practices unless the customer requests segregation.
- **D-3 Invitation role catalog.** Roles referenced by invitations are placeholders until `define-actors-agents-and-work-inbox` publishes its role catalog; this change stores role references, never role definitions.
- **D-4 FD-5 tier mapping.** The §7 proposal needs owner ruling before the specs are written.
