## ADDED Requirements

### Requirement: PG-01 One versioned gateway contract with one canonical schema
openPractice SHALL expose one versioned contract, `openpractice-patient-gateway/1.0`, between HealthLinc and openPractice (rulings 34 and 36). HealthLinc is the owner's working name for the ecosystem patient app.

The canonical schema SHALL be `openpractice-patient-gateway-1.0.schema.json` in this change's `contracts/` directory, identified by the SHA-256 digest of its bytes. Both sides SHALL validate every message against the schema with the recorded digest. Any schema change SHALL produce a new digest, recorded in this change and in the HealthLinc companion change before either side implements it.

The contract SHALL carry a `MAJOR.MINOR` version. Implementations are compatible exactly when they share a MAJOR version. Additive changes increment MINOR. A breaking change needs a new MAJOR version, a new digest and a migration path. Because every schema object is closed, a sender SHALL NOT send a MINOR version that the receiver has not declared it supports. A receiver SHALL refuse a message whose MAJOR version it does not support with reason `unsupported_major_version`, and a message that fails the schema with reason `schema_invalid`, and SHALL change nothing.

#### Scenario: A valid message arrives
- **WHEN** HealthLinc sends a `1.0` `entitlement.request` that validates against the schema with the recorded digest
- **THEN** openPractice processes it and answers under the same contract version

#### Scenario: A message fails the schema
- **WHEN** a message carries a property the schema does not define
- **THEN** the receiver rejects it with `schema_invalid` and changes nothing

#### Scenario: An unsupported major version arrives
- **WHEN** an openPractice that supports only `1.x` receives a `2.0` gateway message
- **THEN** it rejects the message with `unsupported_major_version` and changes nothing

### Requirement: PG-02 HealthLinc reaches openPractice only through the gateway, and openPractice runs without it
HealthLinc SHALL reach openPractice only through the gateway contract, carried on openPractice's versioned API layer (PR-09, constitution Principle V). HealthLinc SHALL authenticate as a named integration identity scoped to the gateway operations; the mechanism is decided in planning. Neither system SHALL read or write the other's database. openPractice SHALL operate fully with no patient app connected, and the gateway SHALL be optional for every deployment.

#### Scenario: A client bypasses the gateway
- **WHEN** a patient-app client tries to read or change openPractice data outside the gateway operations
- **THEN** the request is refused and no data is read or changed

#### Scenario: A deployment has no patient app
- **WHEN** an openPractice deployment connects no HealthLinc instance
- **THEN** registration, scheduling, billing and staff work all operate normally

### Requirement: PG-03 The entitlement check returns coded capabilities, and three are always granted
openPractice SHALL answer each `entitlement.request` with an `entitlement.result` that lists the linked patient's granted capabilities as codes and lists each withheld capability with coded reasons only (rulings 34, 41, 42 and 52). The capabilities are:
- `urgent_escalation`, `billing_contact` and `own_records_access`;
- `clinical_messaging_nonurgent`, `scheduling_new_nonurgent` and `medical_ai_consult`;
- `administrative_questions`, `demographic_change_proposal` and `coverage_change_proposal`.

In this change, "non-urgent" means applied urgency `routine` only (ruling 41; design call DC-30). Anything above `routine` goes through `urgent_escalation`. `medical_ai_consult` covers non-urgent consults with medXfactory's medical AI, which HealthLinc connects the patient to (rulings 51 and 52; design call DC-38). HealthLinc's coach is HealthLinc's own: it has no capability, the result never gates it, and HealthLinc SHALL NOT derive any restriction on it from the result.

For every linked patient, the result SHALL always grant `urgent_escalation`, `billing_contact` and `own_records_access`, whatever the registry status, membership or payment hold. The schema SHALL make those three impossible to withhold. A patient who is active (PG-04) and not on a payment hold SHALL be granted every capability the link's scope allows. A payment hold SHALL withhold only `clinical_messaging_nonurgent`, `scheduling_new_nonurgent` and `medical_ai_consult` (PH-01; rulings 41 and 52), and the schema SHALL refuse the reason `payment_hold` on any other capability. The result SHALL state the link kind.

`own_records_access` covers the patient's records in openPractice. The result MUST NOT carry an amount, a balance or any detail beyond the codes. HealthLinc SHALL apply the result as given and SHALL NOT derive any other restriction from it, including on records another backend holds; this is a HealthLinc companion-change item. When the request names a registered relying party, the result also carries an assertion that the relying party verifies itself (PG-16).

#### Scenario: An active patient with no hold
- **WHEN** HealthLinc asks for the capabilities of an active, linked patient with no payment hold
- **THEN** every capability in the link's scope is granted and nothing is withheld

#### Scenario: A patient on a payment hold
- **WHEN** HealthLinc asks for the capabilities of an active patient on a payment hold
- **THEN** only `clinical_messaging_nonurgent`, `scheduling_new_nonurgent` and `medical_ai_consult` are withheld, each with reason `payment_hold`, and the three always-granted capabilities are present

#### Scenario: A hold reason on another capability
- **WHEN** an `entitlement.result` withholds `administrative_questions` with reason `payment_hold`
- **THEN** the message fails the canonical schema and is rejected

#### Scenario: An implementation tries to withhold urgent escalation
- **WHEN** an `entitlement.result` lists `urgent_escalation` as withheld or omits it from the granted list
- **THEN** the message fails the canonical schema and is rejected

### Requirement: PG-04 Active means an active registry record, plus a current membership where the practice sells memberships
openPractice SHALL treat a linked patient as active only when two conditions hold (ruling 42):
- the patient's registry record is active under change `define-mvp-practice-foundation` (PR-01, PR-11);
- where the practice sells memberships, the patient's membership is current under the entitlement rules of change `define-customer-financial-data-boundaries` (PR #3, not yet on main).

A linked patient who is not active SHALL keep the three always-granted capabilities. Every other capability SHALL be withheld, with reason `registry_inactive`, `membership_not_current` or `membership_entitlement_unresolved`, subject to ruling 50's safeguards below. This extends ruling 34 for patient safety (design call DC-4). How the membership condition applies to a patient with no membership, in a practice that also serves non-members, is design call DC-10.

When openPractice cannot evaluate the status, because a dependency is unavailable or a membership entitlement cannot be resolved, the result SHALL still grant the three. It SHALL withhold every other capability with reason `evaluation_unavailable` or `membership_entitlement_unresolved` (fail closed for everything except safety), subject to ruling 50's safeguards below.

Cut-offs other than a payment hold get safeguards too (ruling 50). They apply to `clinical_messaging_nonurgent` and, because ruling 52 gates medical-AI consults like non-urgent clinical messaging, to `medical_ai_consult` (design call DC-31):
- When the status cannot be evaluated, neither SHALL be withheld for `evaluation_unavailable`. Routine clinical messages still route to openChart, wherever clinical routing is enabled (PG-10).
- A deactivation or a lapsed membership SHALL withhold them only after the patient has been told and a grace period set by an approved, versioned messaging-continuity policy has passed. The medical director approves that policy, and the approval is never opted in for agent signing (DC-28). Until a version is approved, a deactivation or a lapse SHALL NOT withhold them.
- The grace period SHALL start only when openPractice holds a `notice.ack` with outcome `delivered` for the `access_grace_started` notice, or has recorded a delivery through another channel that the registry's contact rules allow (PR-05), with the channel, the time and who recorded it. Without either, nothing SHALL be withheld, and the office manager owns the undelivered notice as work. The grace period runs its full length from that delivery; when that moves its end, openPractice sends a corrected notice (design call DC-41).
- A clinician MAY keep them open for continuity of care with a `messaging_continuity` marker recorded in openChart (ER-18). While the marker is valid, neither SHALL be withheld for a deactivation or a lapse.
- The notice SHALL be a `patient.notice` of kind `access_grace_started`, with `grace_ends_at` and the policy's approved notice template, sent through HealthLinc, or a notice sent through another channel that the patient's confidential-communication instructions allow (PR-05). Its `restriction_codes` carry those instructions, and push or text content carries no PHI, amount or reason (as PH-05). When access returns, openPractice SHALL send a `patient.notice` of kind `access_restored`.

In a practice that sells memberships, a patient who holds no membership SHALL be treated as active on the registry condition alone until the entitlement rules of `define-customer-financial-data-boundaries` are ratified. `membership_entitlement_unresolved` then withholds nothing from that patient. This is design call DC-10, not part of ruling 50.

#### Scenario: A deactivated patient after notice and grace
- **WHEN** a linked patient whose registry record is deactivated asks for capabilities, after the patient was told and the approved grace period has passed, with no valid continuity marker
- **THEN** only `urgent_escalation`, `billing_contact` and `own_records_access` are granted, and the rest are withheld with `registry_inactive`

#### Scenario: A deactivation before any messaging-continuity policy is approved
- **WHEN** a linked patient is deactivated and no messaging-continuity policy version is approved
- **THEN** `clinical_messaging_nonurgent` and `medical_ai_consult` stay granted, and the other non-safety capabilities are withheld with `registry_inactive`

#### Scenario: A membership has lapsed, inside the grace period
- **WHEN** a linked patient of a practice that sells memberships has a membership that is not current, and the grace period has not passed
- **THEN** the three always-granted capabilities, `clinical_messaging_nonurgent` and `medical_ai_consult` remain, and the rest are withheld with `membership_not_current`

#### Scenario: The grace notice is delivered
- **WHEN** HealthLinc answers an `access_grace_started` notice with `notice.ack` outcome `delivered`
- **THEN** the grace period starts at the delivery time, and routine clinical messaging and medical-AI consults stay granted until it ends

#### Scenario: The grace notice fails, then reaches the patient another way
- **WHEN** HealthLinc answers `failed`, and staff record a letter sent to a mailing address that the patient's instructions allow
- **THEN** the grace period starts at the recorded delivery, and nothing is withheld before then

#### Scenario: The grace notice fails and no permitted channel exists
- **WHEN** HealthLinc answers `failed` with `no_permitted_channel`, and the registry's contact rules allow no other channel
- **THEN** the grace period does not start, nothing is withheld, and the office manager owns the undelivered notice

#### Scenario: A clinician keeps messaging open
- **WHEN** a valid `messaging_continuity` marker exists for a patient whose grace period has passed
- **THEN** `clinical_messaging_nonurgent` and `medical_ai_consult` stay granted

#### Scenario: The status cannot be evaluated
- **WHEN** a dependency is unavailable during an entitlement check
- **THEN** the three always-granted capabilities, `clinical_messaging_nonurgent` and `medical_ai_consult` are granted, and the rest are withheld with `evaluation_unavailable`

#### Scenario: A non-member in a membership practice
- **WHEN** a practice that sells memberships serves a patient with no membership, and the entitlement rules are not yet ratified
- **THEN** the patient is treated as active on the registry condition alone, and nothing is withheld with `membership_entitlement_unresolved`

### Requirement: PG-05 Each account link is one link to openPractice, made only after verified identity, with no master identity
openPractice SHALL link a HealthLinc account to an openPractice patient only through `link.request` and `link.result`, and only after verified identity (ruling 39). There SHALL be no master patient identity across HealthLinc, openPractice and openChart. HealthLinc links to each backend through that backend's own link contract. openPractice MUST NOT link, merge or match its patient to an openChart or HealthLinc identity through the gateway.

openPractice SHALL store the HealthLinc account as the triple (HealthLinc issuer, `healthlinc`, value) and compare it as a triple (EB-02). The namespace token `healthlinc` is design call DC-1. HealthLinc SHALL store openPractice's reference as (openPractice issuer, `openpractice`, value). openPractice SHALL mint a `gateway_link_id` for each link.

A link SHALL become `linked` only when both of these hold:
- the request carries a verification record: a coded method, an evidence reference and a time;
- a named human confirms, at tier 2, the registry record that the practice's invitation or an exact match (PR-08) identifies, after seeing it side by side with the identity claim.

The verification mechanism SHALL be decided in planning. Possession of a device, a telephone number or an invitation code SHALL NOT by itself count as verified identity. The system MUST NOT create a link from automatic matching. An agent MAY propose a link confirmation but MUST NOT sign it, because the evidence is patient-submitted, untrusted content (change `define-actors-agents-and-work-inbox` AH-05).

#### Scenario: A verified patient is linked
- **WHEN** a link request carries a verification record and a staff member confirms the exact-match registry record
- **THEN** openPractice answers `linked` with the `gateway_link_id` and the openPractice patient triple, and stores the HealthLinc account triple

#### Scenario: Identity is not verified
- **WHEN** a link request carries only an invitation code that the account holder possesses
- **THEN** openPractice answers `refused` with `identity_not_verified` and creates no link

#### Scenario: An exact match exists but nobody confirms
- **WHEN** a request's claimed name and date of birth exactly match one registry record and no staff member confirms it
- **THEN** the link stays `pending_review` and no capability is served on it

#### Scenario: An agent tries to confirm a link
- **WHEN** a named AI agent tries to sign a link confirmation
- **THEN** the sign-off is refused whatever the opt-in settings, and the link waits for a named human

### Requirement: PG-06 Representative and proxy links follow an approved proxy policy
openPractice SHALL support representative links, where a HealthLinc account acts for a patient as `parent_or_guardian`, `personal_representative` or `caregiver_proxy`. Each SHALL carry a documented authority reference and a granted scope drawn from the capabilities.

A representative link SHALL be created only under an approved, versioned proxy policy (design call DC-12). The practice's medical director approves the policy, and counsel's review is recorded on it. The link also needs the representative's verified identity and a named human's tier-2 confirmation of the authority. Before an approved proxy policy exists, openPractice SHALL refuse every representative link with reason `proxy_policy_missing` (fail closed).

The granted scope SHALL always include `urgent_escalation`. For a representative, `billing_contact` and `own_records_access` reach only what the proxy policy and the patient's confidential-communication instructions (PR-05) allow for that relationship.

A `link.result` that links a representative SHALL carry `representative`: the relationship and the authority reference recorded on the link when the named human confirmed it (design call DC-49, PROPOSED). The same pair travels, verifiable, in the entitlement assertion (PG-16). Both are already recorded on the representative link; only carrying them back out is new. The guarantor relationship that the registry records (PR-06) is a billing fact and SHALL NOT stand in for a representative's relationship or authority. A `link.result` for a patient's own link, or one that is `pending_review` or `refused`, carries no `representative`.

A representative whose proxy policy gives no access to the patient's billing SHALL NOT learn of a payment hold (design call DC-34). For that representative, a capability that a hold withholds SHALL carry reason `representative_scope` instead of `payment_hold`, and no hold-policy reference SHALL be sent. The entitlement result states the link kind and whether billing is in the representative's scope, and the schema enforces this. The same rule SHALL apply to the withheld reasons of an `inquiry.ack`. Each representative link SHALL carry the expiry or review date the policy sets, and SHALL be revoked when the authority ends. This change asserts no legal rule about who may act for a patient; the proxy policy states those rules after counsel's review.

#### Scenario: A parent links for a young child
- **WHEN** a verified parent with documented authority asks to link for a child, and an approved proxy policy covers that relationship
- **THEN** a staff member confirms the authority, and the link is `linked` with the scope the policy grants

#### Scenario: No proxy policy is approved
- **WHEN** a representative link is requested before any proxy policy version is approved
- **THEN** openPractice refuses it with `proxy_policy_missing`

#### Scenario: A caregiver without billing scope while the patient is on hold
- **WHEN** a caregiver proxy whose policy excludes billing asks for capabilities while the patient is on a payment hold
- **THEN** the withheld capabilities carry `representative_scope`, no `payment_hold` reason or hold-policy reference is sent, and a result that reveals the hold fails the schema

#### Scenario: A representative link is confirmed
- **WHEN** a staff member confirms a parent's documented authority and the link becomes `linked`
- **THEN** the `link.result` carries `representative` with relationship `parent_or_guardian` and the authority reference recorded at confirmation, and a result without them fails the schema

#### Scenario: The authority ends
- **WHEN** a representative link reaches its policy expiry date without renewal
- **THEN** the link is revoked with `representative_authority_ended`, and HealthLinc is told

### Requirement: PG-07 Gateway links can be revoked, and revocation wins
Either side SHALL be able to revoke a gateway link with `link.revoked` and a coded reason. Revocation SHALL win over any concurrent confirmation. On revocation, openPractice SHALL stop serving every gateway operation on that link at once. An inquiry already accepted stays routed and owned until it closes (ER-14).

These SHALL each be grounds for revocation: the patient's request, the end of a representative's authority, failed re-verification, and a wrong-patient finding. A patient's own request takes effect at once, and a representative link also ends at its policy expiry date (PG-06). Any other revocation by openPractice SHALL be made by a named human at tier 2. A wrong-patient finding SHALL also raise work owned by the office manager to assess a possible privacy incident; this change asserts no legal conclusion about it. It MUST NOT delete the patient record, the routing records or the audit trail.

#### Scenario: A patient unlinks the app
- **WHEN** a patient asks to unlink and the link is revoked
- **THEN** the next `entitlement.request` on that link is rejected with `link_revoked`

#### Scenario: A wrong-patient link is found
- **WHEN** staff find that a link joined the wrong person
- **THEN** the link is revoked with `wrong_patient`, the office manager receives the assessment work, and no record is deleted

#### Scenario: An urgent item is open at revocation
- **WHEN** a link is revoked while an urgent inquiry from it is still routed
- **THEN** the inquiry stays routed and owned until it closes

### Requirement: PG-08 Patient-submitted demographic changes are proposals, reviewed before they apply
openPractice SHALL receive patient-submitted demographic changes as `proposal.submitted` messages of kind `demographics`. It SHALL record each as a proposal with patient provenance: source `patient_self_service`, the submitting link, and whether the patient or a representative submitted it. It MUST NOT apply a proposal automatically.

Each proposal SHALL become a work item owned by the front-desk lane, with a clock (change `define-actors-agents-and-work-inbox` WI-01, WI-06). A named human SHALL accept or decline it at tier 2 (tension T-4). An agent MAY draft a recommendation but MUST NOT sign the decision, because the proposal is untrusted content (AH-05).

An accepted proposal SHALL be made as an ordinary registry change. Contacts and addresses keep history (PR-04). Confidential-communication instructions follow PR-05. The change is audited (PR-10). For a patient linked to an EHR, it is published under EB-06 with provenance source `patient_self_service` and reason `patient_reported_change`. A declined proposal SHALL be reported with a coded reason.

A proposal that only adds confidential-communication restrictions SHALL be honored by every openPractice patient contact from receipt until it is resolved, without changing the registry record (design call DC-11).

#### Scenario: A patient reports a new address
- **WHEN** a patient proposes a new home address and a front-desk staff member accepts it
- **THEN** the address becomes current from its date, the prior address stays as history, the change is audited, and a linked EHR receives the next demographic version

#### Scenario: A proposal is declined
- **WHEN** a staff member cannot verify a proposed legal-name change and declines it
- **THEN** HealthLinc receives `proposal.resolved` with outcome `declined` and a coded reason, and the registry is unchanged

#### Scenario: Nobody reviews a proposal
- **WHEN** a proposal waits past its clock
- **THEN** its work item escalates under WI-06, stays owned, and nothing is applied

#### Scenario: A patient asks for no home mail
- **WHEN** a patient proposes adding the `no_home_mail` restriction
- **THEN** no statement or letter is mailed to the home address while the proposal awaits review

### Requirement: PG-09 Patient-submitted coverage changes are proposals, reviewed before they apply
openPractice SHALL receive patient-submitted coverage changes as `proposal.submitted` messages of kind `coverage`, with action `add`, `update` or `end`. It SHALL treat each as a proposal under PG-08's rules, owned by the biller lane.

An accepted coverage proposal SHALL be made as a new coverage version effective from a stated date (PR-07). Priorities SHALL stay unique among overlapping coverages. Every version that an eligibility check, estimate, claim or statement used SHALL stay unchanged. The coverage-changed event SHALL be emitted. A proposal that would break PR-07's rules SHALL be corrected by staff or declined, never applied as submitted.

A pending coverage proposal SHALL hold no claim, because claims use the registry's current coverage. Gateway 1.0 SHALL carry no attachments, such as insurance-card images (design call DC-20).

#### Scenario: A patient adds a new plan
- **WHEN** a patient proposes a new coverage and a biller accepts it
- **THEN** a new coverage version takes effect from its start date and the coverage-changed event is emitted

#### Scenario: The proposal conflicts with an existing primary
- **WHEN** a proposed primary coverage overlaps an existing primary coverage
- **THEN** it is not applied as submitted, and the biller corrects the priority or declines it with a coded reason

#### Scenario: A claim is due while a proposal waits
- **WHEN** a visit's claim is ready while a coverage proposal for the same dates awaits review
- **THEN** the claim proceeds on the registry's current coverage and is not held by the proposal

### Requirement: PG-10 Questions enter through inquiry intake, and a clinical question carries no text
openPractice SHALL accept patient questions, and escalations to the patient's human doctor, as `inquiry.submitted` messages (rulings 44 and 51). Each carries:
- HealthLinc's reference for the request;
- where the conversation lives, as `conversation_source`: `healthlinc` for a conversation HealthLinc holds, whether a coach conversation, a direct message to the doctor or an administrative thread, or `medxfactory` for one with medXfactory's medical AI;
- a `conversation_ref` to the conversation in that system, whose namespace matches the source;
- a reply-path reference and a triage reference;
- the proposed category and urgency, with `proposal_source` naming who proposed them: HealthLinc's coach (`coach_triage`), medXfactory's medical AI (`medical_ai_triage`), or HealthLinc's red-flag checklist (`patient_checklist`) (design call DC-45).

The two AIs use the conservative defaults (ruling 51): an uncertain category is proposed as clinical, and an uncertain urgency as urgent. When a patient writes straight to the doctor with no AI triage, HealthLinc asks a short red-flag checklist (ruling 71): any red flag proposes `urgent`, and HealthLinc shows its emergency guidance; no red flag proposes `routine`; a skipped checklist proposes `priority`. The patient may raise that urgency but never lower it, and such a message is clinical. An escalation may originate from any of these sources; either way HealthLinc submits it through the gateway (design call DC-37). openPractice stores no clinical content from any conversation. The medical AI's records reach openChart through MedxEHR, which relays them with receipts; medXfactory does not write to openChart directly, and openPractice is never in that path (rulings 53 and 70).

An administrative inquiry SHALL carry its question text and a topic hint. A clinical inquiry MUST NOT carry question text or a topic hint, and the schema SHALL refuse one that does. This keeps clinical content out of openPractice at intake (ruling 34).

openPractice SHALL answer each inquiry with `inquiry.ack`: either `accepted`, with a routing id and the applied category and urgency, or `rejected`, with a coded reason. It SHALL then route the item under `escalation-routing`. A retry with the same `message_id`, or a new message with the same HealthLinc message reference, SHALL return the same routing id.

Administrative question text SHALL be stored as an administrative communication record under Principle I safeguards: encrypted, access-controlled, and with every read audited. Audit entries SHALL reference the text by id and never copy it (design call DC-15).

Each inquiry needs one capability, judged on its applied urgency (ruling 41; design call DC-30):
- An inquiry whose applied urgency is above `routine`, `priority` or `urgent`, needs only `urgent_escalation`, which is never withheld.
- A routine clinical inquiry needs `clinical_messaging_nonurgent`.
- A routine administrative inquiry with topic `billing_statement`, `payment` or `coverage` needs `billing_contact`, and one with topic `records_request` needs `own_records_access`. Any other routine administrative inquiry needs `administrative_questions`.

An inquiry that needs a withheld capability SHALL be rejected with `capability_withheld` and coded reasons, and HealthLinc SHALL show the patient its emergency guidance and the paths that are always available (companion item). An inquiry with applied urgency above `routine` SHALL never be rejected for a withheld capability (ER-09).

An inquiry with `human_requested` set, because the patient asked for a person or said an answer did not answer the question, SHALL route to the lane's staff and never to a read-only answer (ER-10; design call DC-36).

Clinical inquiry intake, and intake of any inquiry whose applied urgency is `urgent`, SHALL NOT go live until clinical routing is enabled (ER-13). When clinical routing is missing or disabled, openPractice SHALL refuse every inquiry whose applied category is clinical or whose applied urgency is `urgent` with `clinical_routing_unavailable`. It SHALL send HealthLinc `guidance.instruct` for that inquiry's `message_id`, with cause `clinical_routing_unavailable`, and raise an exception owned by the office manager that holds no PHI. Meanwhile the entitlement result SHALL withhold `clinical_messaging_nonurgent` with `clinical_routing_unavailable` (design call DC-33).

#### Scenario: A patient asks a billing question
- **WHEN** HealthLinc submits an administrative inquiry with topic `billing_statement` and routine urgency
- **THEN** openPractice accepts it with a routing id, stores the text under Principle I safeguards, and routes it to the biller lane

#### Scenario: A clinical inquiry carries text
- **WHEN** HealthLinc submits an inquiry proposed as clinical that includes question text
- **THEN** openPractice rejects it with `schema_invalid` and stores none of the text

#### Scenario: A routine clinical message during a hold
- **WHEN** a patient on a payment hold submits a clinical inquiry with routine urgency
- **THEN** openPractice rejects it with `capability_withheld` and reason `payment_hold`

#### Scenario: An urgent clinical message during a hold
- **WHEN** a patient on a payment hold submits a clinical inquiry with urgent urgency
- **THEN** openPractice accepts and routes it

#### Scenario: A priority clinical message during a hold
- **WHEN** a patient on a payment hold submits a clinical inquiry whose applied urgency is `priority`
- **THEN** openPractice accepts and routes it, because it needs only `urgent_escalation`

#### Scenario: An escalation from medXfactory's medical AI
- **WHEN** HealthLinc submits a clinical escalation whose conversation lives in medXfactory, with `conversation_source` `medxfactory`
- **THEN** openPractice routes it to openChart by reference, with no text, and openChart retrieves the conversation from medXfactory through `openchart-conversation-retrieval/1.0`

#### Scenario: Clinical routing is disabled
- **WHEN** an urgent clinical inquiry arrives at a deployment whose clinical routing is disabled
- **THEN** it is refused with `clinical_routing_unavailable`, HealthLinc receives `guidance.instruct` with that cause for the inquiry, and the office manager owns an exception that holds no PHI

### Requirement: PG-11 Administrative replies are relayed to HealthLinc with attribution
openPractice SHALL relay each administrative reply to HealthLinc as `reply.relay`, into the reply path the inquiry named. It SHALL track the reply until HealthLinc answers `reply.ack` with `delivered`.

A reply SHALL be sent only by a staff member in the item's lane (ER-11) or by an agent under ER-10. Each reply SHALL record its attribution (change `define-actors-agents-and-work-inbox` AM-03): the author kind, the author reference, the approving human for an agent draft at `propose`, the answer-type id and version for a read-only answer (change A AH-05 exception 1), and the lane. A reply MUST NOT contain clinical advice. Retries SHALL reuse the original `message_id`. A staff member's explicit send is the tier-3 release decision, so a human-sent reply has no countdown (design call DC-25, tension T-8).

A reply that HealthLinc rejects, or does not acknowledge within the retry policy, SHALL become work owned by the item's lane owner (WI-02). Reply text SHALL be stored and audited like question text (PG-10). HealthLinc SHALL show the patient when an agent wrote a reply (design call DC-18).

#### Scenario: A biller answers a statement question
- **WHEN** a biller sends a reply to a billing inquiry
- **THEN** HealthLinc receives `reply.relay` with author kind `staff` and lane `biller`, and answers `delivered`

#### Scenario: HealthLinc rejects the reply
- **WHEN** HealthLinc rejects a reply because the link was revoked
- **THEN** the reply becomes work owned by the item's lane owner, and nothing is resent automatically to another channel

#### Scenario: A reply is retried
- **WHEN** openPractice retries a reply after a timeout, with the original `message_id`
- **THEN** HealthLinc shows the patient one reply

### Requirement: PG-12 Gateway 1.0 covers the MVP set, and appointments and payments come later
Gateway contract 1.0 SHALL cover exactly:
- the entitlement check;
- account and representative links, and their revocation;
- demographic and coverage proposals;
- inquiry intake, inquiry status and recategorization;
- administrative reply relay;
- the emergency-guidance instruction;
- patient notices (`patient.notice`) and their delivery acknowledgment (`notice.ack`).

It MUST NOT carry appointment or payment operations. Those SHALL be added only as later MINOR versions under a governed change (ruling 36). HealthLinc's own app features stay on HealthLinc's roadmap. The no-account texted pay link of FD-10(b) is unchanged and does not depend on the gateway.

#### Scenario: A plan proposes booking through the gateway
- **WHEN** a feature plan proposes appointment booking under gateway `1.0`
- **THEN** it is out of scope for `1.0` and waits for a governed MINOR version

#### Scenario: A patient without the app pays
- **WHEN** a patient with no HealthLinc account follows a texted pay link
- **THEN** the payment works as FD-10(b) describes, without a gateway link

### Requirement: PG-13 No PHI in gateway logs, errors, notifications, keys or diagnostics
Gateway logs, error messages, diagnostic bundles, push-notification text, idempotency keys and coded reasons MUST NOT contain names, dates of birth, contact details, addresses, member identifiers, amounts, question or reply text, or clinical content (EB-12; change `define-actors-agents-and-work-inbox` AH-04 and AO-12). Diagnostics SHALL reference messages only by `message_id`, `gateway_link_id` and routing id. The idempotency key SHALL be the random `message_id`. Rejections SHALL use only coded reasons.

#### Scenario: A proposal is rejected
- **WHEN** a coverage proposal fails validation
- **THEN** the log and the rejection carry the `message_id`, the `gateway_link_id` and a coded reason, and no member identifier or name

#### Scenario: A reply notification is pushed
- **WHEN** HealthLinc is told a reply is ready
- **THEN** any push text it sends is generic and carries no PHI (companion item), and openPractice sends it no text to push

### Requirement: PG-14 The gateway boundary is covered by contract tests
The gateway boundary SHALL be covered by contract tests, written first (constitution Principle III), that validate every message type against the canonical schema at its recorded digest. They SHALL cover:
- the always-granted capabilities and every withheld reason, including `payment_hold` only on the three capabilities a hold may withhold;
- ruling 50's safeguards for routine clinical messaging and medical-AI consults;
- `representative_scope` in place of a hold for a representative without billing scope;
- the conversation source and reference;
- refusal with `clinical_routing_unavailable` while clinical routing is missing or disabled;
- patient notices: no amount or reason, `grace_ends_at` only on `access_grace_started`, and no countdown or grace period without a confirmed or recorded delivery;
- refusal of every message with `operator_agreement_unverified` until the HealthLinc operator agreement is verified;
- link verification, human confirmation, and refusal without verification;
- refusal of representative links without an approved proxy policy;
- revocation;
- proposal review with no automatic application;
- the no-text rule for clinical inquiries;
- reply relay and retry;
- the no-PHI rules;
- the entitlement assertion of PG-16: its header and decoded claims against their schema definitions, the representative's relationship and authority, `urgent_escalation` always present, no withheld reason, hold or patient reference; and, with a relying-party double, refusal of a tampered, expired, mis-addressed, replayed or substituted assertion (one whose `sub` names another patient's link, presented in a session whose link was bound to a different patient), of a missing or different `nonce` where one was supplied, of any `alg` other than `EdDSA` and any header that names a key location, and of an unknown `kid` after one refresh; a removed `kid` refused after the next scheduled refresh, within one maximum lifetime plus the skew; each accepted `jti` remembered until its `exp` plus the skew; a new link refused until the relying party's own re-enrolment or link verification binds it, and two links of one person each accepted only under the enrolment it is bound to; and urgent access unchanged when no assertion can be verified;
- the cross-field rules the schema cannot check: an assertion whose `sub` differs from the result's `gateway_link_id`, whose `exp` is later than the result's `valid_until` or more than the maximum lifetime after `iat`, or which grants a capability the result withholds, such as `medical_ai_consult` under a payment hold, is never issued and is refused by the relying-party double;
- `representative` on a linked representative `link.result`, and never on any other;
- `inquiry.status` `clinician_acknowledged` (ER-19).

The tests SHALL run against a HealthLinc contract double with synthetic data only. They MUST NOT depend on a live HealthLinc service or real PHI.

#### Scenario: CI runs the gateway suite
- **WHEN** CI runs the gateway contract tests
- **THEN** they pass against a HealthLinc contract double, with synthetic fixtures only

#### Scenario: A fixture looks real
- **WHEN** a reviewer finds a gateway fixture value without a synthetic marker
- **THEN** the fixture is rejected and replaced before merge (PR-14)

### Requirement: PG-15 No gateway capability is enabled for a HealthLinc deployment until its operator agreement is verified
openPractice SHALL enable no gateway capability for a HealthLinc deployment until an authorized person has recorded and verified the agreement that counsel requires with that deployment's HealthLinc operator (design call DC-43). The record follows the pattern of AI-14, AH-02 and VA-01: counterparty, execution date, effective and renewal or expiry dates, scope statement, owner-local document reference, verifying person and time. This change asserts no legal rule; counsel decides what agreement is required and its form. The system MUST NOT assume that any operator offers or has signed such an agreement.

Until the agreement is verified, and at once if it expires or is revoked, openPractice SHALL refuse every gateway message from that deployment with `operator_agreement_unverified`, store nothing from it, and raise an exception owned by the office manager that holds no PHI. Items already accepted stay routed and owned until they close, and an urgent item's fallback keeps running (ER-09). This gate comes before PG-10's clinical-routing gate: while it is unmet, no inquiry is accepted at all; once it is met, clinical and urgent intake still waits for clinical routing. HealthLinc keeps its in-app emergency guidance throughout, and shows it for any refused urgent inquiry (companion item).

#### Scenario: A deployment without a verified agreement
- **WHEN** a HealthLinc deployment whose operator agreement is not verified sends an `entitlement.request` or an `inquiry.submitted`
- **THEN** openPractice refuses it with `operator_agreement_unverified`, stores nothing from it, and the office manager owns an exception with no PHI

#### Scenario: The agreement is verified
- **WHEN** an authorized person records and verifies the operator agreement
- **THEN** the gateway capabilities are enabled for that deployment, and clinical and urgent intake still waits for clinical routing (PG-10)

#### Scenario: The agreement lapses with an urgent item open
- **WHEN** the operator agreement expires while an urgent item from that deployment is routed
- **THEN** new messages are refused with `operator_agreement_unverified`, the routed item stays owned, and its fallback keeps running

### Requirement: PG-16 An entitlement result can carry a signed assertion, so a relying party need not rely on the patient app's statement of the entitlement
A relying party that is not the patient app, such as medXfactory's medical AI before a non-urgent consult (ruling 52), SHALL be able to verify what openPractice granted without relying on the patient app's statement of the entitlement (ruling 93).

**What it proves, and what it does not (design call DC-48, PROPOSED).** An assertion proves what openPractice granted to one gateway link, for one audience, at one time, and that nobody altered it. It does not prove which person is using a session. The patient app still authenticates the patient, and the relying party still binds a link to its own record of the patient, as its checks below require.

**When it is issued.** When an `entitlement.request` names an `assertion_audience` that the practice has registered as a relying party (design call DC-47), openPractice SHALL add an `assertion` to the `entitlement.result`. For an audience that is not registered, openPractice answers as before, with no assertion.

**Wire format (design call DC-46, PROPOSED).** The assertion is a JSON Web Token (RFC 7519) in JWS compact serialization (RFC 7515), built only from open standards:
- its protected header holds exactly `alg` `EdDSA`, with Ed25519 keys only (RFC 8037), `typ` `openpractice-entitlement+jwt`, and `kid`, and validates against the schema's `entitlementAssertionHeader`. It names no key location: no `jku`, `jwk`, `x5u`, `x5c` or `crit`;
- its payload holds the claims below, and validates against the schema's `entitlementAssertionClaims`;
- its signature is the 64-byte Ed25519 signature over the ASCII bytes of the encoded header, a dot and the encoded payload, so it covers exactly the bytes sent and no canonicalization step is needed.

The claims are:
- `iss`: the issuing openPractice deployment's configured issuer (EB-02);
- `sub`: the `gateway_link_id` the assertion is about;
- `aud`: the relying party's configured issuer, as one string;
- `iat` and `exp`: issue and expiry times in NumericDate seconds, with `exp` no later than the result's `valid_until` and no more than the maximum lifetime after `iat`;
- `jti`: a random UUID, unique per assertion;
- `nonce`: the relying party's nonce, echoed when the request carried one;
- `link_kind`, and for a representative link `representative`, with the relationship and the authority reference (PG-06; design call DC-49);
- `capabilities`: the capabilities the result grants, limited to those the practice registered that audience to rely on, always including `urgent_escalation`.

No other claim is allowed. The assertion SHALL carry no PHI and no master identity: no name, date of birth, contact detail, openPractice patient reference, HealthLinc account reference, withheld capability, withheld reason, hold, hold policy, amount or balance. It never reveals a payment hold, to a representative or to a relying party (DC-34). Its `sub` SHALL equal the result's `gateway_link_id`, its `link_kind` the result's `link_kind`, and each of its capabilities SHALL be granted in the result. The schema cannot check those three, so the contract tests do (PG-14).

**Keys (design call DC-46, PROPOSED).** openPractice SHALL publish each practice's verification keys as a JWK Set (RFC 7517) of Ed25519 public keys. Where the JWK Set is published, and the pin a relying party checks it against, are fixed when the relying party is registered; a relying party never takes a key or a key location from a token. Each `kid` is the RFC 7638 SHA-256 thumbprint of its key. A new key SHALL be published before its first use, and a retired key SHALL stay published for at least the maximum lifetime plus the clock-skew tolerance after its last use. A relying party SHALL refresh its copy of the JWK Set from the registered location at least once every maximum assertion lifetime, and SHALL treat a `kid` that is no longer in the published set as unknown. A relying party that meets an unknown `kid` refreshes the JWK Set once from the registered location, and refuses the assertion if the key is still unknown.

**Emergency key removal (design call DC-46, PROPOSED).** When a signing key may be compromised, openPractice SHALL stop signing with it and remove it from the published JWK Set at once, without waiting out the retirement period above. Every assertion signed with that key, including one an attacker mints, then stops verifying at every relying party within one maximum assertion lifetime plus the clock-skew tolerance, because each relying party refreshes the set at least that often and treats the removed `kid` as unknown. Assertions signed with the practice's other keys are unaffected, and urgent access never depends on any of them. Who may remove a key, and how a suspected compromise is reported, are set in planning (Open Question 18).

**Lifetime and skew (design call DC-52, PROPOSED).** An assertion lives at most 15 minutes from `iat` to `exp`, and relying parties allow 60 seconds of clock skew. A payment hold, a lapse, a deactivation or a link revocation that takes effect after an assertion is issued therefore reaches the relying party no later than that assertion's expiry, at most 15 minutes and the skew after its issue. openPractice issues no later assertion that reflects the old state. Urgent access is unaffected.

**What a relying party checks.** A relying party SHALL accept an assertion only when every one of these checks passes:
1. the header validates, `alg` is `EdDSA`, the `kid` names an Ed25519 key in the practice's JWK Set from the registered location, and the signature verifies over the exact bytes received;
2. the decoded payload validates against `entitlementAssertionClaims`;
3. `iss` is the openPractice issuer it registered for that practice, and `aud` is its own issuer;
4. `sub` equals a gateway link that the relying party has itself bound to the enrolment the session belongs to, and is never compared with, or bound from, a link named by the message, request or session that carries the assertion. A binding is per relying-party enrolment and per link: one person may hold several links, such as their own and one as a representative, and each is bound to the enrolment under which it was verified. A link is bound only through the relying party's own enrolment, re-enrolment or link verification, which rests on the patient app's authentication of the patient and which no assertion replaces. A new link, such as the one PG-05 mints when a patient is linked again after a revocation, is refused until the relying party binds it that way (design call DC-48, PROPOSED);
5. the current time is no earlier than `iat` less the skew and earlier than `exp` plus the skew, and `exp` is no more than the maximum lifetime after `iat`;
6. when it supplied a nonce for the session, `nonce` is present and equal to it. An assertion without it, or with another value, fails;
7. `jti` has not been accepted for another session. A relying party SHALL remember each accepted `jti` until that assertion's `exp` plus the skew tolerance; after that, check 5 refuses the assertion anyway.

A failed check means the patient holds no verified non-urgent capability with that relying party. An assertion for another patient's link is refused, and only non-urgent access is withheld.

**When verification cannot be done, urgent access is never withheld.** No assertion, a refused or unregistered audience, a failed check, a signing outage or an unreachable openPractice SHALL withhold only non-urgent capabilities from the relying party's own service. `urgent_escalation` never depends on an assertion: openPractice SHALL accept and route every inquiry whose applied urgency is above `routine` under PG-10 and ER-09 whatever the state of any assertion, and the relying party SHALL hand priority and urgent need on at once (companion item). The absence of an assertion SHALL NOT remove any capability from the result.

**Identifiers every audience sees (design call DC-53, PROPOSED).** Every registered audience receives the same `sub` and, for a representative, the same `authority_ref` for a link, so two relying parties could correlate a patient between their records. This is accepted for 1.0: each relying party is registered by a named human at tier 2 after its operator's agreement is verified, and both identifiers are opaque and carry no PHI. Per-audience identifiers are Open Question 21.

**Registration and records.** Registering or removing a relying party SHALL be a tier-2 action by a named human, the practice manager or the controller or office manager, never an agent (change `define-actors-agents-and-work-inbox` AO-03). Registration records the relying party's issuer, the capability codes it may rely on, and where it reads the practice's JWK Set with its pin, and SHALL wait until an authorized person has recorded and verified the agreement counsel requires with that relying party's operator, following PG-15's pattern (design call DC-47, PROPOSED). openPractice SHALL record each assertion it issues, by `jti`, audience, link and times, with no PHI in any log (PG-13). HealthLinc SHALL pass the assertion on unchanged, and SHALL NOT alter, re-sign or vouch for it (companion item).

#### Scenario: The medical AI verifies a consult entitlement
- **WHEN** HealthLinc asks for capabilities with `assertion_audience` naming the practice's registered medical-AI relying party and a nonce that relying party issued, for a patient whose link the relying party bound at enrolment and who holds `medical_ai_consult`
- **THEN** the result carries a JWT whose `iss`, `aud`, `sub`, `nonce`, times and `capabilities` (`urgent_escalation` and `medical_ai_consult`) pass every check, and the relying party starts the consult

#### Scenario: An assertion is substituted
- **WHEN** a patient app presents, in patient B's session and with B's nonce, an assertion whose `sub` is patient A's link
- **THEN** the relying party refuses it, because `sub` is not the link it bound to B, and B is still handed on at once for any priority or urgent need

#### Scenario: An assertion is altered in transit
- **WHEN** a relying party receives an assertion whose payload was changed after signing
- **THEN** the signature fails, the patient holds no verified non-urgent capability, and any priority or urgent need is still handed on at once

#### Scenario: An assertion is replayed for another audience, after expiry or without the nonce
- **WHEN** an assertion issued for one relying party is presented to another, after its `exp` plus the skew, or without the nonce the relying party supplied
- **THEN** the relying party refuses it, and urgent need is still handed on

#### Scenario: A key the relying party does not know
- **WHEN** an assertion's `kid` is not in the relying party's copy of the practice's JWK Set
- **THEN** the relying party refreshes the JWK Set once from the registered location, and refuses the assertion if the key is still unknown

#### Scenario: A signing key is removed after a suspected compromise
- **WHEN** openPractice removes a signing key from the practice's JWK Set at once, and someone keeps presenting assertions signed with it
- **THEN** each relying party stops accepting them at its next scheduled refresh, no later than one maximum assertion lifetime plus the skew after the removal, and urgent need is still handed on

#### Scenario: A patient is linked again after a revocation
- **WHEN** a patient whose link was revoked is linked again, and an assertion names the new `gateway_link_id`
- **THEN** the relying party refuses it until its own re-enrolment or link verification binds the new link to the patient's enrolment, and never binds the new link because an assertion names it

#### Scenario: One person holds two links
- **WHEN** a person holds their own link and a representative link for a child, and the relying party verified each under its own enrolment
- **THEN** an assertion for either link is accepted only in a session of the enrolment that link is bound to

#### Scenario: A hold starts while an assertion is valid
- **WHEN** a payment hold takes effect five minutes after an assertion granting `medical_ai_consult` was issued
- **THEN** that assertion stays valid only until its `exp`, no later than 15 minutes after issue, and the next result withholds `medical_ai_consult` and issues no assertion granting it

#### Scenario: A representative's assertion
- **WHEN** a caregiver proxy whose granted scope includes `medical_ai_consult` asks for capabilities with a registered audience
- **THEN** the assertion carries `link_kind` `representative`, relationship `caregiver_proxy` and the authority reference, and no hold, withheld reason or patient reference

#### Scenario: The audience is not registered
- **WHEN** an `entitlement.request` names an audience the practice has not registered
- **THEN** the result is answered as before, with no assertion, and no capability changes

#### Scenario: openPractice cannot be reached
- **WHEN** the relying party can obtain no assertion because openPractice is unreachable
- **THEN** no non-urgent consult starts, an urgent need is handed on at once, and openPractice routes it when it arrives
