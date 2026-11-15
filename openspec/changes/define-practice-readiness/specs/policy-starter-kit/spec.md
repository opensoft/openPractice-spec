## ADDED Requirements

### Requirement: SK-01 openPractice ships a starter template for each policy a readiness gate needs
openPractice SHALL ship a starter kit with one template for each policy, profile or plan that a readiness gate needs a practice to approve. The kit SHALL contain exactly these templates (`design.md`, "The starter kit"):
1. the hold policy (PH-03);
2. the proxy policy (PG-06);
3. the routing-clock policy, including the alert-acknowledgment window, the re-send interval and the clinician-acknowledgment alert window (ER-08, ER-09, ER-20);
4. the messaging-continuity policy (PG-04);
5. the on-call coverage plan (ER-13);
6. the autonomy opt-in set: the action types, each with the highest rung AO-04 allows for it, every one starting at `propose` (AO-03, AO-04, BAB-17);
7. the agent rate and blast-radius limit values (AO-08);
8. the revenue-recognition policy: the reporting basis, the estimator, the receivable classification, the materiality approach and the reopening policy, with the approval record for the first estimator version (AI-12, AI-20);
9. the read-only answer types, one for each family of AO-11 (coverage on file, balance, appointment time and practice information), each with its template reference, allowlisted fields, topics, cancel window and sampled-audit rate (AH-05 exception 1);
10. recipient profiles in the single AI-23 shape: for a customer-detail destination (AI-10), for the agent operator and for a model provider (AH-03), and for a business output (`define-customer-financial-data-boundaries`, "Every business output has a scoped recipient profile");
11. the remote VA device, session and location rules (VA-04);
12. the outreach policy (`define-customer-financial-data-boundaries`, "Audience analysis does not authorize outreach");
13. the offering routes and membership entitlements (BAB-16; `define-customer-financial-data-boundaries`, "Offerings determine financial routing at line level");
14. the retention policy profile, with no duration, jurisdiction, program, provider type, effective date or source filled in (`add-reimbursement-retention-control`, "Retention policy profiles are versioned and sourced");
15. the account mapping per destination and posting mode (BAB-06), shipped only once a governed reason taxonomy exists.

Each template SHALL name the gate and the requirement it serves, the authority whose sign-off the policy needs, and every item that the requirement says a version must state. When a later change adds a gate that needs a policy, that change SHALL say whether the kit gains a template; the kit follows the requirements as the checklist does (`readiness-checklist` RC-02). The kit SHALL contain no agreement text: business associate agreements, the operator agreements of PG-15 and PG-16, and every other agreement belong to their parties and to counsel, and the checklist only records them. The kit SHALL contain no consent form either: AH-06 leaves the consent's form and wording to counsel, and no gate approves them.

#### Scenario: A new practice opens the kit
- **WHEN** a practice manager opens the starter kit in a release whose reason taxonomy is not yet governed
- **THEN** fourteen templates are listed, each naming its gate, its requirement, the authority that must sign it and the items a version must state, and the account-mapping template is shown as waiting for the reason taxonomy

#### Scenario: Someone looks for an agreement template
- **WHEN** a user looks in the kit for a business associate agreement or an operator agreement
- **THEN** the kit offers none, and the checklist row for that agreement shows only what its requirement records

### Requirement: SK-02 A template is inert until the named authority signs a policy made from it
A template SHALL never be in force. No gate SHALL read a template: each gate reads only a policy, profile, plan or opt-in version approved under its own requirement. Shipping, installing, upgrading or opening the kit, or copying a template into a draft, SHALL enable nothing. Every gate that needs the policy SHALL stay in its fail-closed state until the authority its requirement names approves a version. For example, PH-03 refuses every hold, PG-06 refuses every representative link, the default windows of ER-08 apply, PG-04 withholds nothing that ruling 50 protects, AI-20 refuses go-live, and AO-04 keeps every action type at `propose`.

#### Scenario: The kit is installed and nothing is approved
- **WHEN** a release with the starter kit is installed and no policy version is approved
- **THEN** every hold placement is refused, every representative link is refused, and no accounting destination can go live

#### Scenario: A draft exists but is not approved
- **WHEN** a practice manager copies the hold-policy template into a draft and nobody approves it
- **THEN** every hold placement is still refused under PH-03

### Requirement: SK-03 Signing a template is the same act, with the same record, as signing a policy written from scratch
To use a template, an authorized person SHALL create a practice draft from it. The draft is the practice's own text and may be changed freely. It SHALL become a version only when the authority its requirement names approves it through that requirement's own approval action, with the same tier, signer rules, completeness check, counsel's review where the requirement needs it, refusal of agent signing, re-authentication on mobile (MA-07) and audit as a version written from scratch. The approved version SHALL record the identifier and version of the template it started from, as provenance only (design call RD-6). That provenance SHALL confer nothing and SHALL change no check. Every gate SHALL treat a version made from a template exactly as it treats a version written from scratch. Approving a draft left unchanged from its template SHALL still be an explicit approval of the full text the approver was shown.

#### Scenario: The medical director approves an unchanged template
- **WHEN** the medical director approves a hold-policy draft left unchanged from its template, with counsel's review recorded on it
- **THEN** the version is approved through PH-03's own action, recorded exactly as a version written from scratch, and carries the template's identifier and version as provenance

#### Scenario: A template-based draft leaves a required item blank
- **WHEN** a hold-policy draft made from the template leaves the treatment of open GFE dispute windows blank
- **THEN** it cannot be approved, exactly as PH-03 refuses any version that omits a required item

#### Scenario: An agent tries to approve a template-based policy
- **WHEN** a named AI agent tries to approve a routing-clock draft made from the template
- **THEN** the approval is refused whatever the opt-in settings, as ER-08 requires

### Requirement: SK-04 Nothing is adopted by default or in bulk
No template SHALL be adopted on installation, on upgrade, on first run or by default, and no approval SHALL cover more than one version. The system SHALL offer no action that adopts or approves several templates, policies, profiles or opt-ins at once, and SHALL refuse any request to do so. Each opt-in SHALL be its own sign-off, for one agent and one action type, under AO-03 and AO-04. Opting one action type up raises no other, and the set shows only the highest rung AO-04 allows, never a suggested rung. A person who holds several authorities SHALL approve each version separately, in the role its requirement names (`signer-roles` SR-02). No agent SHALL approve a version made from a template or adopt an opt-in, and no such approval SHALL be opted in for agent signing (BAB-17, AO-03).

#### Scenario: Someone asks to accept every starter policy
- **WHEN** a user asks to adopt every template in the kit at once
- **THEN** the request is refused, and nothing is adopted

#### Scenario: An upgrade brings new templates
- **WHEN** a release upgrade installs a newer kit
- **THEN** no template is adopted and no practice version changes

#### Scenario: One person approves two policies
- **WHEN** a physician who is the medical director approves a hold-policy version and a routing-clock policy version on the same day
- **THEN** each is its own approval, through its own requirement's action, recording the medical director role

#### Scenario: One action type is opted up
- **WHEN** the practice manager opts one agent up for one action type, to a rung no higher than AO-04 allows for it
- **THEN** only that action type for that agent changes, and every other action type stays at `propose`

### Requirement: SK-05 Templates are versioned, and a new template version never changes a policy in force
Each template SHALL carry an identifier and a version, and a published template version SHALL never change; any change is a new version. A new template version, a release upgrade or the withdrawal of a template SHALL NOT change, re-base, withdraw or re-approve any practice draft or version, including one started from an earlier version of that template. When a newer version of a template exists, the checklist SHALL show it beside each version started from an earlier one (design call RD-8). Taking up anything from the newer template SHALL need a new version approved under SK-03, like any other change. Withdrawing a template SHALL NOT withdraw any practice version, so it SHALL NOT cause a lift such as the one PH-09 makes when a hold-policy version is withdrawn.

#### Scenario: A new hold-policy template version is published
- **WHEN** a new version of the hold-policy template is published while a practice's approved hold policy started from the earlier version
- **THEN** the practice's approved version stays in force unchanged, and the checklist shows that a newer template exists

#### Scenario: A template is withdrawn
- **WHEN** a template version is withdrawn from the kit while active holds rely on a version started from it
- **THEN** the practice's version and the holds stay as they were

#### Scenario: A practice takes up a newer template
- **WHEN** a practice wants the wording of the newer hold-policy template
- **THEN** it makes a new draft, records counsel's review, and the medical director approves it as a new version under PH-03

### Requirement: SK-06 Templates assert no legal, accounting or clinical rule
Each template SHALL carry a statement that it is a starting point for the named authority's review, that openPractice asserts no legal, accounting or clinical rule by shipping it, and which review the policy needs before approval. A template SHALL contain no jurisdiction-specific duration, no legal citation offered as a rule, no accounting election and no clinical standard. It MAY show a value that an existing requirement already states, such as the default windows of ER-08, marked as that requirement's value; every other value SHALL be left for the practice to fill (design call RD-7). The retention policy profile template SHALL leave the duration, jurisdiction, program, provider type, effective date and authoritative source empty, so that it can never act as the fallback duration that `add-reimbursement-retention-control` forbids. This change writes no template content; who drafts and reviews that content before it ships is an open question for the owner.

#### Scenario: The routing-clock template shows defaults
- **WHEN** a practice opens the routing-clock template
- **THEN** it shows the acknowledgment and response windows that ER-08 already states, each marked as ER-08's fail-closed default, and leaves every other value empty

#### Scenario: The revenue-recognition template
- **WHEN** a practice opens the revenue-recognition template
- **THEN** each of the five items AI-20 names is a question for the practice's accountant, with no option chosen

#### Scenario: The retention template
- **WHEN** a practice opens the retention policy profile template
- **THEN** it has no duration, jurisdiction, program, provider type, effective date or source, and no obligation can be calculated from it
