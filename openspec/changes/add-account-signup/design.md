# Account signup

## First release

| Step | Actor | Result |
|---|---|---|
| Start now | Guest | A work email is submitted and a verification email is queued when eligible |
| Verify work email | Mailbox holder | An explicit confirmation marks the request verified |
| Send practice invite | QA operator | Frappe creates a Website User with the existing Practice Administrator role and queues its native welcome email |
| Set password | Invited administrator | Frappe signs the user in and opens New Practice |
| Set up practice | Authorized administrator | Practice onboarding owns the office, legal entity, and openHR Company connection |

The form asks only for the work email. Mailbox verification proves control of an address. The operator decides whether it belongs to the intended practice administrator. The address is normalized consistently with Frappe's case-insensitive account lookup. Existing registered addresses receive the same public request response and are ineligible for a new invite through this path.

## Request and mail handling

The persistent request records the email, issuance time, verification outcome, and operator who issued the invite. Guest and practice-user roles have no direct read or write access to this register. The verification link expires after 30 minutes. A new message supersedes the previous link, with a two-minute resend interval and a five-per-24-hour window per address. The guest endpoints are also rate-limited.

Use the configured QA origin in email links. A verification token is sent in the URL fragment and submitted in the body only after the user confirms. Store only the current token digest in the request record and clear it upon verification. Email queue access is operator-controlled. Opening or previewing the link does not consume it. Replaying a verified link or issuing an already-issued invite does not repeat the transition.

Lock the request when changing verification or invite state. Use the existing native welcome email and standard password setup. An existing or concurrently created User follows the site's access process and retains its current roles. An invite and its request state must commit together. Delivery errors after a successful enqueue are retried through the existing mail queue.

## Activation boundary

This release runs only on a dedicated site with synthetic practice and openHR data. The signup flag is disabled by default. Only the configured QA HTTPS origin or a local loopback site can enable it. The site must disable Frappe's unrestricted signup and configure its default outgoing account and email queue. A Frappe System Manager operates the QA invite queue. Production activation is outside this release. The only role assigned through signup is the existing openPractice practice administrator role. The first-run path uses the existing access checks for New Practice. Session desk, patient records, billing, and evidence roles are independently controlled. No practice, openHR Company, claim, or patient is created during signup.

The future activation gates are scoped separately. A practice's approved intake and applicable countersigned BAA govern access to that practice's real patient data. An authorized user's MFA and practice membership are additional requirements before such access. Billing readiness and payer enrollment are evaluated for the billing entity and payer. One practice reaching intake or a billing milestone grants no access to other practices in the same workspace.

## Later account and workspace decisions

| Topic | Decision to resolve before that work |
|---|---|
| Account register (`account-register`) | Identity provider, MFA and recovery policies, lifecycle, staff invitations, and subscription ownership |
| Workspace (`workspace`) | Tenant provisioning, region and residency, authorization across workspace and practice memberships, and the single-practice or MSO model raised in `add-practice-onboarding` O-3 |
| Account-to-practice membership (`account-practice-link`) | Owner and member roles, explicit acceptance of assisted claims, transfer, revocation, and audit |
| Activation | Binding rules between an authorized user, practice intake status, BAA coverage, and the entity and payer billing gate |
| FD-5 | Review ownership transfer, account closure, suspension and assisted claims against the authentic approval tiers before implementing them |

Accounts, workflow actors, credentialed providers and schedulable staff are distinct record types. The same person can appear in several of them, linked by stable identifiers appropriate to each system. Later work must define those links without assuming that a user credential implies staff eligibility, clinician credentials, or financial authority.
