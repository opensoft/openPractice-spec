Status: draft

## Why

Practice onboarding starts when an authorized administrator reaches New Practice. A new practice needs a way to request that access, prove control of a work mailbox, and accept an invitation. Signup establishes the administrator's login. Practice onboarding establishes the organization and governs activation for real patient data and billing.

## What Changes

- Add Start now to the openPractice QA front door. Request the administrator's work email and verify control of that mailbox.
- Let a QA operator review verified requests and issue the existing Frappe invitation. Completing the invitation signs the administrator in and opens New Practice.
- Limit this slice to an isolated synthetic QA site and the existing practice setup role. Onboarding, openHR connection, patient-data activation, and billing readiness remain governed by their owning capabilities.

## Capabilities

### New Capabilities

- `account-signup` (AS-01 through AS-04) covers the public request, email verification, operator-issued invite, sign-in handoff, and QA boundary.

### Modified Capabilities

None.

## Impact

The QA site gains a signup request register and a restricted operator queue. Frappe owns credentials, the invitation and password-setup flow, and sessions. The existing New Practice flow owns the office and legal entity. The QA signup path creates a single practice-administrator identity on an existing site. The future account register, workspace and account-to-practice model are described in the design for review.
