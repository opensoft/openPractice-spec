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

The hosted acceptance in task 1.5 remains open. The target hostname is `openpractice.opensoft.dev` and the intended sender domain is `opensoft.one`. The hosted QA backend reached Microsoft 365 SMTP with TLS and OAuth advertised. A configured outgoing sender identity and working DNS and TLS routing for the target hostname are required for the hosted run.

## 2. Future scope

- [ ] 2.1 Resolve and specify workspace provisioning, practice membership, MSO tenancy, and cross-practice authorization.
- [ ] 2.2 Resolve and specify identity provider, MFA and recovery, staff invitation, lifecycle, and subscription ownership.
- [ ] 2.3 Resolve and specify assisted claiming, transfer, revocation, audit, and practice-scoped activation gates alongside `add-practice-onboarding`.
- [ ] 2.4 Record owner ratification for the exact agreed revision before creating a governed Speckit feature.
