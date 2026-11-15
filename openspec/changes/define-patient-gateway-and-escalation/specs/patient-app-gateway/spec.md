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
openPractice SHALL answer each `entitlement.request` with an `entitlement.result` that lists the linked patient's granted capabilities as codes and lists each withheld capability with coded reasons only (rulings 34, 41 and 42). The capabilities are:
- `urgent_escalation`, `billing_contact` and `own_records_access`;
- `clinical_messaging_nonurgent` and `scheduling_new_nonurgent`;
- `administrative_questions`, `demographic_change_proposal` and `coverage_change_proposal`.

For every linked patient, the result SHALL always grant `urgent_escalation`, `billing_contact` and `own_records_access`, whatever the registry status, membership or payment hold. The schema SHALL make those three impossible to withhold. A patient who is active (PG-04) and not on a payment hold SHALL be granted every capability the link's scope allows. A payment hold SHALL withhold only `clinical_messaging_nonurgent` and `scheduling_new_nonurgent` (PH-01).

`own_records_access` covers the patient's records in openPractice. The result MUST NOT carry an amount, a balance or any detail beyond the codes. HealthLinc SHALL apply the result as given and SHALL NOT derive any other restriction from it, including on records another backend holds; this is a HealthLinc companion-change item.

#### Scenario: An active patient with no hold
- **WHEN** HealthLinc asks for the capabilities of an active, linked patient with no payment hold
- **THEN** every capability in the link's scope is granted and nothing is withheld

#### Scenario: A patient on a payment hold
- **WHEN** HealthLinc asks for the capabilities of an active patient on a payment hold
- **THEN** only `clinical_messaging_nonurgent` and `scheduling_new_nonurgent` are withheld, each with reason `payment_hold`, and the three always-granted capabilities are present

#### Scenario: An implementation tries to withhold urgent escalation
- **WHEN** an `entitlement.result` lists `urgent_escalation` as withheld or omits it from the granted list
- **THEN** the message fails the canonical schema and is rejected

### Requirement: PG-04 Active means an active registry record, plus a current membership where the practice sells memberships
openPractice SHALL treat a linked patient as active only when two conditions hold (ruling 42):
- the patient's registry record is active under change `define-mvp-practice-foundation` (PR-01, PR-11);
- where the practice sells memberships, the patient's membership is current under the entitlement rules of change `define-customer-financial-data-boundaries` (PR #3, not yet on main).

A linked patient who is not active SHALL keep the three always-granted capabilities. Every other capability SHALL be withheld, with reason `registry_inactive`, `membership_not_current` or `membership_entitlement_unresolved`. This extends ruling 34 for patient safety (design call DC-4). How the membership condition applies to a patient with no membership, in a practice that also serves non-members, is design call DC-10.

When openPractice cannot evaluate the status, because a dependency is unavailable or a membership entitlement cannot be resolved, the result SHALL still grant the three. It SHALL withhold every other capability with reason `evaluation_unavailable` or `membership_entitlement_unresolved` (fail closed for everything except safety).

#### Scenario: A deactivated patient opens the app
- **WHEN** a linked patient whose registry record is deactivated asks for capabilities
- **THEN** only `urgent_escalation`, `billing_contact` and `own_records_access` are granted, and the rest are withheld with `registry_inactive`

#### Scenario: A membership has lapsed
- **WHEN** a linked patient of a practice that sells memberships has a membership that is not current under that change's entitlement rules
- **THEN** the three always-granted capabilities remain, and the rest are withheld with `membership_not_current`

#### Scenario: The status cannot be evaluated
- **WHEN** the membership entitlement cannot be resolved during an entitlement check
- **THEN** the three always-granted capabilities are granted, and the rest are withheld with `membership_entitlement_unresolved`

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

The granted scope SHALL always include `urgent_escalation`. For a representative, `billing_contact` and `own_records_access` reach only what the proxy policy and the patient's confidential-communication instructions (PR-05) allow for that relationship. Each representative link SHALL carry the expiry or review date the policy sets, and SHALL be revoked when the authority ends. This change asserts no legal rule about who may act for a patient; the proxy policy states those rules after counsel's review.

#### Scenario: A parent links for a young child
- **WHEN** a verified parent with documented authority asks to link for a child, and an approved proxy policy covers that relationship
- **THEN** a staff member confirms the authority, and the link is `linked` with the scope the policy grants

#### Scenario: No proxy policy is approved
- **WHEN** a representative link is requested before any proxy policy version is approved
- **THEN** openPractice refuses it with `proxy_policy_missing`

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
openPractice SHALL accept patient questions as `inquiry.submitted` messages (ruling 44). Each carries HealthLinc's message reference, a reply-path reference, a triage reference, and HealthLinc's AI proposals for category and urgency.

An administrative inquiry SHALL carry its question text and a topic hint. A clinical inquiry MUST NOT carry question text or a topic hint, and the schema SHALL refuse one that does. This keeps clinical content out of openPractice at intake (ruling 34).

openPractice SHALL answer each inquiry with `inquiry.ack`: either `accepted`, with a routing id and the applied category and urgency, or `rejected`, with a coded reason. It SHALL then route the item under `escalation-routing`. A retry with the same `message_id`, or a new message with the same HealthLinc message reference, SHALL return the same routing id.

Administrative question text SHALL be stored as an administrative communication record under Principle I safeguards: encrypted, access-controlled, and with every read audited. Audit entries SHALL reference the text by id and never copy it (design call DC-15).

Each inquiry needs one capability. An urgent inquiry needs `urgent_escalation`. A non-urgent clinical inquiry needs `clinical_messaging_nonurgent`. An administrative inquiry with topic `billing_statement`, `payment` or `coverage` needs `billing_contact`, and one with topic `records_request` needs `own_records_access`. Any other administrative inquiry needs `administrative_questions`.

An inquiry that needs a withheld capability SHALL be rejected with `capability_withheld` and coded reasons, and HealthLinc SHALL show the patient the paths that are always available (companion item). An inquiry with applied urgency `urgent` SHALL never be rejected for a withheld capability (ER-09).

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

### Requirement: PG-11 Administrative replies are relayed to HealthLinc with attribution
openPractice SHALL relay each administrative reply to HealthLinc as `reply.relay`, into the reply path the inquiry named. It SHALL track the reply until HealthLinc answers `reply.ack` with `delivered`.

A reply SHALL be sent only by a staff member in the item's lane (ER-11) or by an agent under ER-10. Each reply SHALL record its attribution (change `define-actors-agents-and-work-inbox` AM-03): the author kind, the author reference, the approving human for an agent draft at `propose`, and the lane. A reply MUST NOT contain clinical advice. Retries SHALL reuse the original `message_id`. A staff member's explicit send is the tier-3 release decision, so a human-sent reply has no countdown (design call DC-25, tension T-8).

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
- the emergency-guidance instruction.

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
- the always-granted capabilities and every withheld reason;
- link verification, human confirmation, and refusal without verification;
- refusal of representative links without an approved proxy policy;
- revocation;
- proposal review with no automatic application;
- the no-text rule for clinical inquiries;
- reply relay and retry;
- the no-PHI rules.

The tests SHALL run against a HealthLinc contract double with synthetic data only. They MUST NOT depend on a live HealthLinc service or real PHI.

#### Scenario: CI runs the gateway suite
- **WHEN** CI runs the gateway contract tests
- **THEN** they pass against a HealthLinc contract double, with synthetic fixtures only

#### Scenario: A fixture looks real
- **WHEN** a reviewer finds a gateway fixture value without a synthetic marker
- **THEN** the fixture is rejected and replaced before merge (PR-14)
