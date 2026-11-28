Status: draft

## 1. Specs

- [ ] 1.1 Author capability spec `account-register` (identity, email verification, MFA, plan, invitations, lifecycle)
- [ ] 1.2 Author capability spec `workspace` (tenant container, region, data mode, tenancy shape)
- [ ] 1.3 Author capability spec `account-practice-link` (create-or-claim, single owner, revocation)
- [ ] 1.4 Write the signup-vs-onboarding split statement and gate chain into the specs index

## 2. Data contracts

- [ ] 2.1 Canonical schemas: account identity, credential reference, invitation, workspace, account-practice link, gate transition record
- [ ] 2.2 SHA256SUMS for all contracts; synthetic fixtures only

## 3. Security and verification

- [ ] 3.1 Password KDF policy document (algorithm, work factors, versioning)
- [ ] 3.2 MFA enrollment ceremony and recovery path (recovery codes, staff-assisted reset tiering)
- [ ] 3.3 Email verification flow with expiry and re-issue
- [ ] 3.4 SSO (OIDC/SAML) reference format for enterprise and MSO accounts

## 4. Gates and enforcement

- [ ] 4.1 Synthetic-only data mode enforcement at the workspace layer
- [ ] 4.2 Gate transition records wired to `add-practice-onboarding` intake and billing gates
- [ ] 4.3 Demo content generator contracts (synthetic fixtures as runtime demo data)

## 5. Links and reconciliation

- [ ] 5.1 White-glove claim flow (staff-created practice claimed by owner account) with tier-2 confirmation
- [ ] 5.2 User-to-provider reference format where a person is both user and provider (per design.md 4)
- [ ] 5.3 Update `add-practice-onboarding` design.md to remove O-3 on reconciliation once D-2 is ruled here
- [ ] 5.4 Publish the four-population reconciliation (accounts, actors, providers, staff) as a cross-reference note in `define-actors-agents-and-work-inbox`

## 6. Closure

- [ ] 6.1 Owner rulings on D-1, D-2, D-3, D-4
- [ ] 6.2 Strict validation pass
- [ ] 6.3 `owner-ratification.md` completed before any Speckit feature
