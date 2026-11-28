## ADDED Requirements

### Requirement: AS-01 Request signup with a work email

The dedicated synthetic QA site SHALL expose Start now and accept an administrator work email. For a syntactically valid address the guest response SHALL be the same regardless of whether the address is unknown, previously requested, or already registered. Signup SHALL NOT create a Frappe User, practice, patient, Company, or claim at this step.

#### Scenario: Start signup from the QA front door
- **WHEN** a guest submits an eligible work email
- **THEN** openPractice records a pending request and queues a verification message to that address

#### Scenario: Repeat a recent request or enter an existing User's address
- **WHEN** a guest resubmits an address before the resend interval or supplies an existing User's address
- **THEN** the response matches the normal accepted response
- **AND** no additional message is queued and existing access is unchanged

### Requirement: AS-02 Confirm control of the mailbox

Verification SHALL require an explicit confirmation using a single-use, expiring token. Reissue SHALL invalidate the previous token. Signup SHALL enforce guest rate limits and per-address email quotas. The request register SHALL store only the current token digest and SHALL clear it on successful verification.

#### Scenario: Open and confirm a valid link
- **WHEN** a recipient opens a valid link
- **THEN** the request stays pending until the recipient confirms
- **AND** confirmation marks the request verified exactly once

#### Scenario: Use an expired, replaced, or already used link
- **WHEN** the recipient confirms with an expired, replaced, or consumed token
- **THEN** the request's verification and invitation state stays unchanged

### Requirement: AS-03 Issue the Frappe invitation and land on New Practice

Only a QA operator SHALL issue an invitation from a verified request. The invitation SHALL use the site's native Frappe welcome and password setup flow. A successful issue SHALL record the operator and SHALL create one Website User with only the practice setup role assigned by signup. A successful first sign-in through the invitation SHALL open New Practice.

#### Scenario: Invite and sign in as a newly verified administrator
- **WHEN** an operator invites a verified address with no existing account and the invitee completes Frappe password setup
- **THEN** the invitee arrives signed in at New Practice and can begin authorized practice setup

#### Scenario: Repeat, issue early, or encounter an existing account
- **WHEN** an invite request is repeated, targets an unverified request, or finds a User already present
- **THEN** no duplicate account or additional grant is created
- **AND** a guest and an ordinary practice administrator cannot issue invites

### Requirement: AS-04 Keep the first release isolated to synthetic QA

The public signup and invitation endpoints SHALL be disabled by default. A site enabling them SHALL be dedicated to synthetic QA. A signup SHALL confer neither production membership nor patient, scheduler, or billing privileges. Practice onboarding SHALL remain independently authorized.

#### Scenario: Attempt to use signup on an unconfigured site
- **WHEN** a guest loads Start now or calls a signup endpoint on a site without the required QA configuration
- **THEN** signup is unavailable and no email or user is created

#### Scenario: Try to use operations as an invited signup user
- **WHEN** a newly invited administrator opens New Practice and then tries to access the signup register or operational patient or billing APIs
- **THEN** New Practice is available and protected register and operational APIs deny access
