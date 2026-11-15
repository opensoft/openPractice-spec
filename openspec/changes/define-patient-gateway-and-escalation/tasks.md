## 1. Owner Ratification Gate

- [ ] 1.1 Run `openspec validate define-patient-gateway-and-escalation --strict --no-interactive` against the exact artifacts proposed for ratification. Acceptance evidence: `owner-ratification.md` records the command, OpenSpec version, timestamp, exact artifact revision or checksums, exit code 0, and the output.
- [ ] 1.2 Obtain the owner's unconditional ratification before any Speckit feature is created. Rulings 34 to 54 do not state a handoff shape; design call DC-21 proposes shape A, the ordered multi-feature handoff in section 2, as a stated exception to the workflow protocol's one-feature handoff. Acceptance evidence: the owner, not an agent, completes `owner-ratification.md`. That means identity, timestamp, exact revision, strict-validation evidence, the chosen handoff shape, his disposition of the design calls, and the unqualified decision `APPROVED UNCONDITIONALLY`. In the same revision, the owner changes the proposal's `Status` to `ratified`.
- [ ] 1.3 Confirm the stacked dependencies are ratified first. Acceptance evidence: `owner-ratification.md` records the ratified revisions of `define-mvp-practice-foundation` and `define-actors-agents-and-work-inbox`, and confirms that the FD-1 amendment (ruling 38) and the FD-10(b) amendment (ruling 36) landed in `ideation/review/openpractice-framing-decisions.md`. A missing dependency blocks feature creation.
- [ ] 1.4 Compare this change with each required companion change (rulings 40, 47 to 49 and 93): `define-clinical-message-inbox` in `opensoft/openChart-spec`, `define-practice-gateway-integration` in `opensoft/HealthLinc`, and, in `MedxSoft/medXfactory`, `define-care-organization-hermes-openpractice-operations` and `define-patient-medical-ai-connection`. For `opensoft/openChart-spec`: the clinical-handoff schema digest (`sha256sum -c contracts/SHA256SUMS`, matched against the digest the companion cites), the contract identifier, the namespaces (`healthlinc`, `medxfactory`, `openpractice`, the EHR's own), the issuer handling, the clinical-need marker, the identity states with acceptance of uncertain urgent and priority offers (MI-18), clinician urgency raises, the `patient_checklist` source, `clinician_acknowledged` (ER-19), and the cited `openchart-conversation-retrieval/1.0` contract (ruling 69). For HealthLinc: the gateway schema digest, the contract identifier, the `healthlinc` namespace, the issuer, the entitlement semantics, the conversation source, `proposal_source` and the red-flag checklist (ruling 71), patient notices and `notice.ack`, the operator agreement of PG-15, the entitlement assertion passed on unchanged (PG-16), the representative's relationship and authority from `link.result` (PG-06), and `clinician_acknowledged` in `inquiry.status` (ER-19). For medXfactory's `define-care-organization-hermes-openpractice-operations`: the read-only answer types and work-item operations in agent-operations 1.0, openPractice's consent check (change A AH-06), the MINOR that carries the other gateway action types, the ladder and the pause switch, and no clinical answering. For medXfactory's `define-patient-medical-ai-connection`: the `medxfactory` conversation references, verification of the entitlement assertion before any non-urgent consult with urgent need never withheld (PG-16), and the medical AI's records reaching openChart through MedxEHR, outside openPractice. Acceptance evidence: each revision compared, each digest pair and result recorded in `owner-ratification.md`. A mismatch with an existing companion blocks feature creation. A companion that does not yet exist blocks only live interoperation; features may build against contract doubles.

## 2. Speckit Features That May Follow, In Order (Handoff Shape A, If Ratified)

Each feature is gated on tasks 1.2 and 1.3 and is created from the assembly root `opensoft/openPractice` on its base branch.

- [ ] 2.1 **Feature 1: patient gateway, openPractice side.** Owns PG-01 to PG-16. Gate: tasks 1.2 and 1.3, the patient-registry feature of `define-mvp-practice-foundation` merged, and change A's work-inbox feature merged. Clinical and urgent inquiry intake goes live only after Feature 2's clinical routing is enabled; until then, PG-10's defensive refusal is active. Acceptance evidence:
  - contract tests validate every message against the gateway schema at its recorded digest, against a HealthLinc double;
  - the always-granted capabilities and every withheld reason are tested, including evaluation failure, `payment_hold` only on the three capabilities a hold may withhold, and `representative_scope` for a representative without billing scope;
  - ruling 50's safeguards: notice, grace period, the clinician's continuity marker, and no cut-off of routine clinical messaging on evaluation failure;
  - priority inquiries need only `urgent_escalation`;
  - the conversation source and reference for HealthLinc-held and medical-AI conversations, and `proposal_source`, including `patient_checklist`;
  - refusal with `clinical_routing_unavailable`, with the guidance instruction and the office-manager exception;
  - refusal of every message with `operator_agreement_unverified` until the HealthLinc operator agreement is verified, with the office-manager exception;
  - patient notices and `notice.ack`, with no grace period started before a confirmed or recorded delivery;
  - link verification, human confirmation, refusal without verification, and agent sign-off refusal are tested;
  - representative links are refused without an approved proxy policy, and a linked representative's `link.result` carries the relationship and authority reference;
  - entitlement assertions: JWS compact tokens whose header and decoded claims validate, issued only for a registered relying party, with no PHI, withheld reason or hold; verified, and refused when tampered, expired, mis-addressed, replayed, substituted, missing the supplied nonce, signed with another algorithm or under an unknown key after one refresh, or signed with a removed key after the next scheduled refresh, by a relying-party double; `jti` retention until `exp` plus the skew; a new link refused until the relying party binds it itself; the three cross-field rules; and urgent access unchanged without one;
  - proposals never auto-apply, and interim confidential-communication restrictions are honored;
  - clinical inquiries with text are refused;
  - the PHI safeguard plan, security review and risk-analysis update are recorded.

  Live interoperation waits for the HealthLinc companion change to be ratified and to match (task 1.4).
- [ ] 2.2 **Feature 2: escalation routing and clinical handoff, openPractice side.** Owns ER-01 to ER-20. Gate: Feature 1 merged, the EHR-link feature of `define-mvp-practice-foundation` merged, and change A's actor-model and work-inbox features merged. Acceptance evidence:
  - contract tests validate every handoff message against the clinical-handoff schema at its recorded digest, against openChart and HealthLinc doubles;
  - the reference-only payload is tested, including refusal of any content field;
  - urgency is never lowered, by any path;
  - every identity state: urgent and priority items offered as `link_in_review` or `unlinked` with only the three snapshot fields, and routine items on an uncertain link kept as exceptions;
  - clinician urgency raises: acknowledgment, the shadowed deadline, the urgent path and no lowering;
  - red-flag checklist proposals (`urgent`, `routine`, `priority` when skipped) applied by rule, a patient's raise sent with source `patient_checklist`, and no lowering;
  - every alert trigger, including `clock_expired` and silence past `respond_by`, the urgent fallback with its guidance instruction, tracked alerts with re-sending and escalation to the medical director, the priority alert, and the no-on-call fallback are tested;
  - rejected urgent inquiries raise a PHI-free exception and a guidance instruction;
  - the clinical-need marker, its attribution and the urgent-origin check;
  - `clinician_acknowledged`: recorded, acknowledged, passed to HealthLinc only while the item is open, a late one not forwarded, a nurse owner covered, and changing no alert, fallback, retry or clock;
  - a recategorization cancels every pending agent answer on the item;
  - Hermes never decides its own draft, and read-only answers follow change A's AH-05 exception 1;
  - the link precondition, re-routing, splitting and text removal are tested, including a late split's continuation, accepted only for the same link, one per resubmission request or one per clinical handoff with no open request, and otherwise rejected with `routing_unknown`, the reminder the lane's owner may reopen, its ordering limit, its urgency without ER-09 while it stays administrative and with ER-07 to ER-09 once routed as clinical, the lane reminder and its alert, and the operator's deletion receipt, its interim channel and its deadline (ER-12);
  - the office manager's alert for an urgent item with no clinician acknowledgment in time is tested, and changes no routing, clock, fallback or retry (ER-20);
  - the pause switch sends administrative items to staff and leaves urgent routing untouched;
  - clinical routing cannot be enabled without full on-call cover (DC-27).

  Hermes read-only answers are enabled only after change A's AH-01 gate, including the AH-02 operator and provider BAAs, and each answer type's approval and switch-on. Hermes drafting is enabled only after the agent-operations MINOR (DC-22). Live interoperation waits for the openChart and HealthLinc companion changes (task 1.4).
- [ ] 2.3 **Feature 3: payment hold.** Owns PH-01 to PH-09. Gate: Feature 1 merged, and patient receivables available from `define-revenue-cycle-core` features 1 and 2: the thread contract, the money ledger, and the guarantor-account balances and balance events of PS-01 and PS-09 (that change is a draft not yet on `main`; this gate is its conforming amendment PA-8, PROPOSED). Acceptance evidence:
  - placement refused with no approved policy, and refused for an incomplete policy version;
  - the hold withholds only the three capabilities of PH-01 and never the three always-granted ones, nor HealthLinc's coach;
  - PH-01's scheduling clause applies only with a ratified scheduling change, and a clinician's marker or a verified urgent escalation always lets an appointment through;
  - tier-3 placement with countdown and cancel, tier-2 lift, and agent ceilings;
  - patient notice before effect, honoring PR-05, with no PHI in push or text content; the countdown starts only on a `delivered` `notice.ack` or a recorded delivery through a permitted channel, and never without one;
  - disputed amounts and open GFE windows never count;
  - no claim, statement or posting is held;
  - automatic lift when the basis ends.

  Holds stay refused in production until the medical director approves a counsel-reviewed policy version (Open Question 3).
- [ ] 2.4 **Feature 4: mobile approver.** Owns MA-01 to MA-07. Gate: change A's work-inbox feature merged; Feature 2 merged before alerts are enabled. Acceptance evidence:
  - every action goes through the versioned API, with server-side checks;
  - notifications carry no PHI, and a deep link opens an item only after a face, fingerprint or passkey verification on an enrolled device, or the device's unlock code where neither face nor fingerprint is available, with face or fingerprint asked first, skipped within 60 seconds of the last one on the server's clock, and never after a typed password;
  - enrolment and recovery never rest on a typed password alone, follow DC-60's paths, with a replacement or recovery waiting 24 hours behind a cancellable notice that also reaches recently revoked devices and removed contact points, a cancellation raising an exception, a second person confirming only with a verification on their own enrolled device, a one-person first device needing codes from two contact points, both qualifying by age, stolen or compromised markings ending notices at once, a lost recovery code declared and notified, and no access when no path exists; a person whose device can complete no counted check is sent to the workstation;
  - no PHI remains on the device after logoff, idle timeout or revocation;
  - lost-device revocation through AM-05;
  - sign-offs follow FD-5 and BAB-17, each with its own verification answering a single-use, two-minute challenge and an assertion bound to the item, the decision and the time, tier 4 human-only and D8 accountant-only;
  - no clinical content is shown.

## 3. Planning Gates For Every Feature

- [ ] 3.1 Perform a meaningful Constitution Check against constitution v1.0.0 before each feature's plan phase. Acceptance evidence: the plan maps Principles I to VI and the quality gates to concrete obligations, records how T-5, T-6 and T-7 were dispositioned, and records the open questions routed to it in 3.5.
- [ ] 3.2 Complete a documented security review and update the living risk analysis for each feature. Acceptance evidence: the review record and the risk-analysis diff are referenced from `closure-evidence.md`.
- [ ] 3.3 Confine runtime changes to the openPractice code leg. Acceptance evidence: the plan's inventory shows no runtime edits in HealthLinc, openChart, MedxFactory or any vendor system.
- [ ] 3.4 Keep this checklist governance-only. Acceptance evidence: executable tasks exist only in each feature's Speckit `tasks.md`, and `speckit-analyze` reports no unresolved critical inconsistency.
- [ ] 3.5 Route every open question (design.md "Open Questions") to the work it blocks. Acceptance evidence: each feature's plan records how its question was answered, or why the work it blocks is out of that feature's scope:
  - Open Question 1, identity verification: blocks Feature 1's link go-live.
  - Open Question 2, proxy policy: blocks representative links only; Feature 1 records the refusal path.
  - Open Question 3, hold policy: blocks placing holds only; Feature 3 records the refusal path.
  - Open Question 4, holds across a guarantor's dependents: blocks Feature 3's placement logic for a guarantor with several patients.
  - Open Question 5, a patient with no EB-01 link: owned by the openChart companion; Feature 2 records the exception path.
  - Open Question 6, the on-call roster's source: blocks enabling clinical routing in Feature 2.
  - Open Question 7, routing-clock windows: blocks nothing; Feature 2 uses the defaults until a policy is approved.
  - Open Question 8, ruling 42 in a mixed membership practice: blocks only that practice's non-safety capabilities; Feature 1 records the fail-closed result.
  - Open Question 9, AI-authorship disclosure: blocks Hermes drafting in Feature 2 until counsel answers or the owner accepts DC-18.
  - Open Question 10, retention of administrative communication records: blocks Feature 1's go-live until a retention decision is recorded.
  - Open Question 11, the agent-operations MINOR: blocks Hermes drafting in Feature 2 only.
  - Open Question 12, HealthLinc name clearance: blocks nothing in `1.x`; the token stays fixed.
  - Open Question 13, the second alert channel and alert windows: blocks enabling clinical routing in Feature 2; the defaults apply meanwhile.
  - Open Question 14, the scheduling change and the grace period: blocks PH-01's scheduling clause, and blocks only withholding under ruling 50, which stays off until a policy is approved.
  - Open Question 15, the `medxfactory` token: blocks live interoperation with medXfactory conversations only.
  - Open Question 16, the HealthLinc operator agreement: blocks enabling the gateway for any HealthLinc deployment (PG-15).
  - Open Question 17, distinguishing a demographic hold from a linkage conflict: blocks nothing here; a follow-up change owns it (T-11).
  - Open Question 18, the assertion's pin form, lifetime, skew and registration record: blocks issuing assertions in Feature 1; every other gateway function proceeds, and urgent access never depends on it.
  - Open Question 19, an alert on a missing clinician acknowledgment: closed by ruling 101; Feature 2 builds ER-20, with the window of DC-58.
  - Open Question 20, the PROPOSED calls DC-46 to DC-53: block the features that build them until the owner dispositions them at ratification.
  - Open Question 21, per-audience identifiers: blocks nothing in 1.0 (DC-53).
  - Open Question 22, the on-call alert for a missing clinician acknowledgment: blocks nothing; ER-20 alerts the office manager only.
  - Open Question 23, a split that openChart asks for: blocks nothing in openPractice; the lane reminder covers the splits openPractice asks for, and a split openChart asks for has none until a later handoff version reports it.
  - Open Questions 24 and 25, synced passkeys and the phone's unlock code: closed by rulings 134 and 135; MA-04 carries them.
  - Open Question 26, how strong workstation sign-in is: closed by ruling 136; change A's AM-07 carries it.

## 4. Acceptance and Closure

- [ ] 4.1 Complete implementation and verification only through the features in section 2. Acceptance evidence: `closure-evidence.md` maps every requirement and scenario to accepted evidence.
- [ ] 4.2 Re-verify cross-repository consistency at landing. Acceptance evidence: `closure-evidence.md` records that the schema digests, contract identifiers, namespaces and issuer handling in the landed code match each companion's landed implementation, or records the companion's work as pending.
- [ ] 4.3 Land each feature's leg pull requests and the assembly root pin bump. Acceptance evidence: pull-request identifiers, exact heads, check results and review dispositions are recorded.
- [ ] 4.4 Run final strict validation on the final revision. Acceptance evidence: the command, version, timestamp, revision and output are recorded, with exit code 0.
- [ ] 4.5 Archive only after 4.1 to 4.4 are complete for Features 1 to 4, and move the canonical schemas to the spec repository's `contracts/` directory with their digests unchanged. Acceptance evidence: the archive date and path, the schema move with matching digests, and the delta-spec sync disposition are recorded.
