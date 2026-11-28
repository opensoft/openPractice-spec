Status: draft

## Why

Change `add-practice-onboarding` governs the data a practice must supply before it can bill, but nothing in openPractice defines how the people who use it come to exist inside it. `define-mvp-practice-foundation` treats the practice throughout as ambient, single-tenant context: it governs "the practice's books" and a "practice bank account register," uses the term *billing entity* — the join key of its payer deposit links — without ever defining one, and maps its own patient register onto "Slice 0's 'party and place register'," which it calls "the substrate that every later slice references" while explicitly declining to ratify that register. In short: the MVP is single-customer software by assumption, and the substrate that makes it multi-practice does not exist anywhere.

Signup (account acquisition) and onboarding (practice activation) are two distinct contracts with different audiences, durations and failure modes:

- Signup answers "who can log in, and who pays the SaaS bill" — minutes-long, self-serve, low-friction, person-scoped.
- Onboarding answers "is this a legitimate healthcare business, and can it bill payers" — days to weeks, business-scoped, verification-heavy (already governed by `add-practice-onboarding`, which delivers the Slice-0 register).

Conflating them is the standard failure in health platforms: adding KYB fields to signup kills conversion; adding signup's permissiveness to onboarding leaks compliance risk. This change defines the signup side and the gate chain between the two.

Three load-bearing facts today:

- `add-practice-onboarding` assumes a practice record exists inside a tenant but nothing defines the tenant or how one is created. Its open decision O-3 (practice vs MSO tenancy) is a tenancy question, and tenancy originates at signup — that decision is moved here (see design.md, D-2).
- The BAA placement is currently implicit. Signup must not grant PHI access: the executed BAA boundaries where real patient data may enter the system.
- `define-actors-agents-and-work-inbox` governs actors, but the account/identity substrate those actors authenticate against is undefined. The repo now holds four overlapping people-populations (accounts, actors, credentialed providers, schedulable staff); none of the existing documents reconciles them (see design.md, §4).

## What changes

- **`account-register`.** Login identities: email + credential or SSO, email verification, required MFA for any account that can reach PHI, plan/subscription reference for SaaS billing, account lifecycle (`active`, `suspended`, `closed`), and invitations for additional staff users.
- **`workspace`.** The tenant container created at signup: workspace key, region, governed data domain for everything created inside it, and the tenancy shape decision absorbed from `add-practice-onboarding` O-3. This is the tenant the MVP change assumes but never defines.
- **`account-practice-link`.** The 1:N mapping between an account and practice records from `practice-register`: create-or-claim flow, white-glove path (staff-created practice later claimed by its owner account), revocation with audit, and a single owner per practice with role-bearing members.
- **Gate chain.** Three governed states sequence what an account can do: signed-up (demo workspace, synthetic data only), `intake-complete` (countersigned BAA + NPPES checks from `add-practice-onboarding` — real patient records unlock), `billing-ready` (per billing entity — charge finalization and claims unlock, unchanged from the onboarding change).

## Capabilities

### New Capabilities
- `account-register` (AC-01 to AC-08): identity creation, email verification, MFA requirement, plan reference, invitations, lifecycle, audit.
- `workspace` (WS-01 to WS-06): tenant container creation, region, synthetic-only demo mode, tenancy shape, audit.
- `account-practice-link` (AP-01 to AP-07): create-or-claim, white-glove claim, single owner, revocation, audit.

### Modified Capabilities

None. This change depends on, and does not modify, `add-practice-onboarding` (gates, practice records) and adopts its open decision O-3. It references `define-actors-agents-and-work-inbox` for actor identity and `define-mvp-practice-foundation` for audit, FD-5 tiers and the synthetic-fixture rule, which this change extends to the entire signed-up-but-not-onboarded workspace.

## Impact

- **New data contracts:** account identity, credential reference (opaque; passwords exist only as versioned KDF hashes), invitation, workspace, account-practice link, gate transition record.
- **PHI exposure:** an account that has not reached `intake-complete` operates on synthetic data only; the repo's synthetic-fixture convention becomes an enforcement point, not just a test rule. The countersigned BAA at the intake gate is the PHI boundary.
- **People-population distinction:** accounts (this change), actors (`define-actors-agents-and-work-inbox`), credentialed providers (`provider-roster`) and schedulable staff (`add-staff-scheduling-and-hr-integrations`) are four disjoint populations; design.md §4 states the reconciliation pointers.
- **Terminology boundary:** nothing in this change touches "re-enrollment" as used by `billing-accounting-boundary` (re-routing payer deposit bank destinations); EDI enrollment status is owned by `add-practice-onboarding`'s `enrollment-tracker`.
- **Governance:** audit of every state change; FD-5 tiering reconciled per design.md §7 (proposed, pending owner ruling); no vendor BAA claims.
