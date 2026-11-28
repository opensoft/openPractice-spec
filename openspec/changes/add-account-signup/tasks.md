## 1. First QA slice

- [x] 1.1 Add the signup requirements and validate the draft with strict OpenSpec.
- [x] 1.2 Add Start now, email verification and a restricted queue for verified requests.
- [x] 1.3 Issue the existing Frappe invitation from that queue and route first sign-in to New Practice.
- [x] 1.4 Test verification, invitation, sign-in and authorization with a synthetic mailbox. Cover duplicate requests, expiry and reissue, replay, an existing User, and operator-only approval.
- [ ] 1.5 Validate on the dedicated QA host with its outgoing mail path, using a controlled mailbox, and record proof that the invited administrator lands on New Practice.

### Acceptance evidence

On 2026-11-28, the disposable Frappe QA site used a synthetic mailbox and real SMTP to an isolated mail capture service. The browser completed verification, the operator sent a native Frappe invitation, and the invitee set a password, arrived signed in at New Practice, and saved a synthetic office. The run also checked duplicate requests, existing-account behavior, expiry and reissue, replay, operator-only approval, access denials, and the disabled feature flag.

- [GitHub Actions signup acceptance for code revision 0158295](https://github.com/opensoft/openPractice-code/actions/runs/46140893501)

The tested revision is staged in QA ACR as `apps/open-practice:signup-0158295` at digest `sha256:4749b39228ac51df45026b62fe01accd4ab34af2e1d77617b507d998a4a792fd`.

On 2026-11-28, Spaceship DNS and verified HTTPS served `openpractice.opensoft.dev`. The QA edge and workloads applied tenant revision `ad46b15bf324e5d279ecbb161a5bd8cc4b43a09f` from [PR #484](https://github.com/opensoft/Opensoft-Tenant/pull/484). The five PracticeLab deployments ran the staged digest. The site backup and migration completed, including the Signup Request DocType.

The hosted Edge run `05113f0314` verified receipt of both Frappe-generated messages in the dedicated Microsoft 365 QA inbox using a mailbox-scoped Graph handoff for this test. The invitee completed native password setup, landed signed in at `/new-practice`, and saved a synthetic office.

Task 1.5 remains open for automatic delivery. Frappe has `openpractice-qa@opensoft.one` configured as its default outgoing account. Exchange rejected the mailbox-scoped SMTP connection with `535 5.7.3`. Public signup was disabled again after the assisted test and `/start` returned HTTP 404.

## 2. Future scope

- [ ] 2.1 Resolve and specify workspace provisioning, practice membership, MSO tenancy, and cross-practice authorization.
- [ ] 2.2 Resolve and specify identity provider, MFA and recovery, staff invitation, lifecycle, and subscription ownership.
- [ ] 2.3 Resolve and specify assisted claiming, transfer, revocation, audit, and practice-scoped activation gates alongside `add-practice-onboarding`.
- [ ] 2.4 Record owner ratification for the exact agreed revision before creating a governed Speckit feature.
