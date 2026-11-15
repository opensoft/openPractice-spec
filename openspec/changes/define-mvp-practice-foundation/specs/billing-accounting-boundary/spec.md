## ADDED Requirements

### Requirement: BAB-01 openPractice owns billing and is the receivable source of truth
openPractice SHALL own claims, patient statements, payer receivables, patient receivables, payment allocation, refunds, collections and the financial audit lineage that connects them. openPractice SHALL be the source of truth for every receivable balance and every payment allocation.

#### Scenario: The balances disagree
- **WHEN** the accounting system shows a different balance for a guarantor account than openPractice does
- **THEN** openPractice's balance is authoritative and the difference appears as a reconciliation exception

### Requirement: BAB-02 The accounting system is the books of record, and openPractice never becomes a ledger
The connected accounting system SHALL be the books of record for the general ledger, chart of accounts, bank balances, the full bank reconciliation, taxes, payroll, payables and the final financial statements. openPractice MUST NOT implement any of the following:
- a general ledger or chart-of-accounts authoring;
- accounts payable or payroll;
- tax computation;
- financial-statement production;
- full bank reconciliation.

#### Scenario: A user looks for a balance sheet
- **WHEN** a user looks for a balance sheet, vendor payables or a full bank reconciliation in openPractice
- **THEN** openPractice offers none, and directs the user to the accounting system

### Requirement: BAB-03 The accounting destination mirrors and never feeds back as truth
The destination SHALL hold a mirror of what openPractice posts. A destination-side change to an openPractice-posted document SHALL be reported as a reconciliation exception. openPractice MUST NOT import that change as receivable or allocation truth.

#### Scenario: A bookkeeper edits a mirrored invoice
- **WHEN** a bookkeeper changes the amount of an openPractice-posted invoice
- **THEN** the next reconciliation report lists the mismatch and openPractice's receivable is unchanged

### Requirement: BAB-04 Posted financial records are immutable and corrected by dated reversal
Once a financial record is posted in openPractice, it SHALL NOT be edited or deleted. A correction SHALL be a new dated reversal, with a replacement where needed, that references the record it corrects. Balances SHALL be answerable as of any past date. openPractice SHALL NOT impose a period-close lock (FD-3).

#### Scenario: A payment was applied to the wrong visit
- **WHEN** staff correct last week's payment allocation
- **THEN** a dated reversal and a new allocation are appended, both referencing the original, and an as-of query for last week still shows the original

### Requirement: BAB-05 Export batches are versioned and immutable for every destination
FD-3's export-batch freeze SHALL apply to every accounting destination. A released batch SHALL be frozen and identified by its batch sequence and content digest. Later corrections SHALL land in a subsequent batch, with back-references to the batch and posting they correct.

#### Scenario: A correction follows a released batch
- **WHEN** a reversal is posted for a posting in a released batch
- **THEN** the released batch is unchanged and the reversal appears in a later batch with a back-reference

### Requirement: BAB-06 The reason taxonomy is the join key, mapped per destination and posting mode
Every openPractice ledger entry SHALL carry exactly one reason category, from a taxonomy defined before any entry posts. Each destination SHALL map reason categories to its existing accounts through an account mapping that is versioned per destination and per posting mode. A batch SHALL be refused release if any of its lines has no mapping in the batch's destination-and-mode mapping version.

#### Scenario: An unmapped reason category
- **WHEN** a batch about to be released holds a line whose reason category has no mapping for that destination and mode
- **THEN** release is refused and the missing mapping becomes work

### Requirement: BAB-07 The integration records money and never moves it
The accounting integration SHALL record financial events and MUST NOT instruct the destination, or any bank or processor, to originate a payment, transfer or refund. Refunds SHALL be decided in openPractice and posted to the destination only as records of issued refunds.

#### Scenario: A refund is approved
- **WHEN** the owner approves a patient refund in openPractice
- **THEN** the destination receives a refund record only after the refund is recorded as issued, and no payment instruction is sent

### Requirement: BAB-08 Legacy receivables are excluded, but a mixed deposit still balances
Balances represented by old-system balance threads (FD-9) SHALL NOT be posted to a destination as customer-detail receivables. When a deposit combines legacy collections with openPractice money, the legacy share SHALL post to a mapped legacy-collections account, so that the deposit entry equals the bank line. The bookkeeper decides which account that is.

#### Scenario: A wire mixes legacy and new money
- **WHEN** a payer wire of 500.00 pays 320.00 on openPractice claims and 180.00 on legacy run-off claims
- **THEN** the deposit entry equals 500.00: 320.00 releases from clearing and 180.00 posts to the mapped legacy-collections account, and no legacy receivable is created

### Requirement: BAB-09 openPractice keeps a register of every practice bank account
openPractice SHALL keep a practice bank account register holding every account the practice uses for money that openPractice tracks: operating, deposit, lockbox, card-processor settlement and any other type. Each entry SHALL carry:
- a label, the bank, and the account type;
- the masked account number;
- the mapped bank account in the accounting system;
- a status and effective dates.

Register changes SHALL be dated, kept as history, and audited.

#### Scenario: A lockbox account is added
- **WHEN** an authorized user adds a lockbox account effective from a date
- **THEN** the register holds it with label, bank, type, masked number, mapped accounting account, status `active` and its effective date

### Requirement: BAB-10 Full bank account and routing numbers are encrypted, restricted and never exported
openPractice SHALL store full bank account and routing numbers encrypted at rest. They SHALL be visible only to the owner and billing-administrator roles, and masked everywhere else. Every view of a full number SHALL be audited. Full numbers MUST NOT appear in logs, error messages, exports, accounting postings, idempotency keys or diagnostic bundles.

#### Scenario: A front-desk user opens the register
- **WHEN** a front-desk user views the bank account register
- **THEN** only masked numbers are shown

#### Scenario: The owner reveals a full number
- **WHEN** the owner reveals a full account number
- **THEN** it is shown and the view is audited with actor and time

#### Scenario: A posting is built for a deposit
- **WHEN** a deposit posting for a register account is assembled
- **THEN** it references the register entry and the mapped accounting account, and contains no full account or routing number

### Requirement: BAB-11 Payer deposit links record how and where each payer pays
openPractice SHALL keep a payer deposit link for each payer and billing entity, where the billing entity is identified by TIN plus NPI. Each link SHALL carry:
- the payment method: `eft`, `check` or `virtual_card`;
- the linked practice bank account;
- an enrollment status: `requested`, `pending`, `active` or `rejected`;
- the payer's enrollment reference and confirmation evidence;
- an effective-dated history.

Deposit enrollment SHALL use the same enrollment-case lifecycle as provider participation enrollment. Deposit enrollment MUST NOT hold a claim; only participation enrollment gates claims. An `active` link sets the expected payment method and the expected account for that payer's money.

#### Scenario: A payer approves EFT
- **WHEN** a payer confirms EFT enrollment for a billing entity into the operating account
- **THEN** the link becomes `active`, with the enrollment reference and evidence, and that payer's wires are expected in the operating account

#### Scenario: A payer has not enrolled the practice for EFT
- **WHEN** a deposit link is `pending`
- **THEN** claims to that payer are not held on that account, and that payer's payments are expected by check

### Requirement: BAB-12 Deposits landing in an unexpected or closed account are flagged
openPractice SHALL check every revenue deposit it explains against the payer's active deposit link and the account's register status. It SHALL flag a deposit that lands in an account other than the payer's linked account, or in an account whose status is closed, as work owned by a Roster entry.

#### Scenario: A wire lands in the wrong account
- **WHEN** a payer wire arrives in the card-settlement account while the payer's link names the operating account
- **THEN** the deposit is flagged as landing in an unexpected account

#### Scenario: Money lands in a closed account
- **WHEN** a deposit arrives in an account the register marks closed
- **THEN** it is flagged as landing in a closed account

### Requirement: BAB-13 A bank change produces a tracked re-enrollment worklist
When a practice bank account is replaced or closed, openPractice SHALL create a re-enrollment worklist. The worklist SHALL hold one item for every payer deposit link that names the old account, and SHALL track each item until that payer confirms the new account. An item SHALL clear only when the payer's confirmation is recorded on the deposit link.

#### Scenario: The practice changes banks
- **WHEN** the operating account is replaced by a new account
- **THEN** every payer linked to the old account appears on the re-enrollment worklist, and each item clears only when that payer's confirmation of the new account is recorded

### Requirement: BAB-14 openPractice reconciles deposits only, is the single poster of each deposit it explains, and leaves the bank reconciliation to the accounting system
openPractice SHALL reconcile only revenue-related bank lines: deposits, chargebacks, payer takebacks, processor fees and refund disbursements. It SHALL label every other bank line "accounting system's" and SHALL NOT reconcile it. For each deposit it explains, openPractice SHALL post exactly one deposit entry to the destination, equal to the bank line, and SHALL be that deposit's single poster (ruling 5): no other route or system SHALL post a deposit entry for the same bank line. When the deposit also funds balances that another route owns, such as membership or retail obligations under change `define-customer-financial-data-boundaries`, that share SHALL post as an `other_route_share` line to the account mapped for it in the destination-and-mode mapping version (BAB-06). The owning route SHALL record only its own obligation allocation. Each refund disbursement line SHALL be matched to the issued refund it pays out (BAB-07). The accounting system SHALL own bank balances and the full bank reconciliation.

#### Scenario: A payer wire is explained
- **WHEN** openPractice explains a payer wire by its remittance trace number
- **THEN** it posts one deposit entry equal to the bank line, which the accounting system's reconciliation can match

#### Scenario: A card settlement funds medical and membership balances
- **WHEN** a card settlement of 150.00 funds a 100.00 medical patient payment and a 50.00 membership invoice payment
- **THEN** openPractice posts one deposit entry equal to 150.00: 100.00 releases from clearing and 50.00 posts as `other_route_share` to its mapped account; the membership route records its allocation and posts no deposit entry

#### Scenario: A refund is paid out of the bank
- **WHEN** a 25.00 refund disbursement appears on an account openPractice reads
- **THEN** openPractice matches it to the issued refund recorded with `refund.post`, and an unmatched disbursement becomes work

#### Scenario: A rent debit appears
- **WHEN** a rent debit appears on an account openPractice reads
- **THEN** it is labelled "accounting system's", openPractice does not reconcile it, and no posting is made for it

### Requirement: BAB-15 Closed destination periods are never edited, and late charges are flagged
openPractice SHALL never post into, or edit, a period the destination reports closed, and SHALL never edit a released batch. The posting date of a charge posting (an invoice, debit note or credit note) SHALL be the date of service when that period is open. Otherwise it SHALL be the first open date, the date of service SHALL be kept on the line, and the posting SHALL be flagged as a late charge. A payment, refund, deposit or other posting SHALL use its own event or bank date (AI-11), under the same closed-period rule. A posting the destination refuses after release because its period closed SHALL be superseded by a new posting in a later batch, without editing the original.

#### Scenario: A charge arrives after the destination closed October
- **WHEN** a charge for an October 28 date of service is assembled after the destination closed October
- **THEN** its posting date is the first open date, its line keeps October 28 as the date of service, and it is flagged as a late charge

### Requirement: BAB-16 Charge finalization checks the offering route before any medical invoice
Before openPractice finalizes a charge for an invoice under AI-12, or for an insurance claim, it SHALL check the service's offering route and the patient's membership entitlement, as change `define-customer-financial-data-boundaries` defines them. A service whose approved membership terms include it without a separate charge SHALL NOT be invoiced or claimed as medical billing. A discounted or separately chargeable service SHALL be charged under its approved terms. An unresolved route or entitlement SHALL hold finalization as work owned by a Roster entry. Until offering routes are configured, every service SHALL route as medical billing, except that a practice that sells memberships SHALL hold each member's charge this way until its routes are configured. The hold's FD-5 deadline escape SHALL release the charge at tier 3 as medical billing when it reaches the payer's filing threshold. The release SHALL notify staff, run the tier-3 cancel window, and record its provenance.

#### Scenario: A membership-included visit
- **WHEN** a member's visit is a service that the membership's approved terms include without a separate charge
- **THEN** openPractice creates no medical invoice and no insurance claim for it

#### Scenario: The entitlement is unresolved
- **WHEN** a member's visit cannot be resolved as included, discounted or separately chargeable
- **THEN** finalization is held as owned review work, and nothing is invoiced or claimed

#### Scenario: The hold reaches the filing threshold
- **WHEN** a held member charge is still unresolved at the payer's filing threshold
- **THEN** it releases at tier 3 as medical billing, staff are notified with a cancel window, and the release records its provenance

### Requirement: BAB-17 Sign-offs follow the FD-5 tiers; a named AI agent signs only where opted in and never at tier 4
Every action this change defines SHALL follow its FD-5 tier (ruling 33). Its tier decides whether it needs an approval, a notification or a delay-and-cancel release, including the deadline escapes. Automation SHALL only propose. Where a tier, or a gate this change defines, calls for an approval, a release decision or an attestation, the signer SHALL be a named human or a named AI agent identified by an openWallet ID (opensoft/openWallet). An agent MAY sign an action type only after that action type is opted in for agent signing, and no action type SHALL be opted in by default. Tier-4 actions SHALL be signed by a named human only. They are hard delete, enabling `customer-detail` mode, refunds leaving the practice, material adjustments and record merges. The D8 approval and every estimator-version approval SHALL be given by the practice's accountant only, and SHALL NOT be opted in for agent signing (accounting-integration AI-12, AI-20). How an accountant from outside the practice holds a roster entry is open with the owner as decision 141: change `define-actors-agents-and-work-inbox` AM-01 and AM-02 give no actor type or recipe for one. Until it is settled, these approvals wait. Every sign-off SHALL record the signer's identity, including the openWallet ID for an agent, with the action, the time and the provenance. How an openWallet identity is verified SHALL be decided in planning.

#### Scenario: An agent signs an action type that is not opted in
- **WHEN** a named AI agent tries to release an export batch, and batch release is not opted in for agent signing
- **THEN** the sign-off is refused and the batch waits for an authorized signer

#### Scenario: An agent tries to sign a tier-4 action
- **WHEN** a named AI agent tries to approve a refund leaving the practice or to enable `customer-detail` mode
- **THEN** the sign-off is refused whatever the opt-in settings, and only a named human may sign

#### Scenario: An opted-in agent signs
- **WHEN** batch release is opted in for agent signing and a named AI agent releases a batch
- **THEN** the release is recorded with the agent's name, its openWallet ID, the time and the provenance

### Requirement: BAB-18 Every openPractice contract carries money in one format
Every `openpractice-*` contract SHALL carry an amount as decimal text plus a currency code, as `openpractice-accounting-integration/1.0` already does (AI-02): a JSON string with an explicit leading minus sign when negative, never a JSON number, together with the amount's ISO 4217 alphabetic currency code, which travels on the object that holds the amount or on the object that holds its lines. openPractice SHALL store amounts in whole cents (ruling 103).

These rules are design calls under ruling 103 (F-8 to F-10, PROPOSED):
- an amount carries exactly its currency's minor-unit digits, and is stored as a whole number of that currency's minor units. Version 1.0 of every contract supports USD only, so two decimal places and whole cents, and a 1.0 message in any other currency is refused. Supporting another currency needs a governed version change under AI-01, which states its minor-unit digits from ISO 4217; a currency with no minor unit carries no decimal point (F-8);
- openPractice has one rounding rule, and each contract states that it uses it. An amount is rounded only when openPractice computes it, such as an estimate or an allocation, and only once, at computation, to the minor unit, half away from zero. When a fixed amount, such as a deposit, a payment, a fee or a bank line, is split into parts, the split SHALL use cumulative rounding: taking the parts in the split's stated order, each part is the rounded running total of the exact shares through that part, minus the rounded running total through the part before it. Where a split names no order, its parts are taken in ascending order of their own identifiers. Shares and running totals SHALL be computed in exact decimal or rational arithmetic, never in binary floating point. The parts then always equal the whole, each part is within one minor unit of its exact share, and no part has the opposite sign to the whole when every share has the whole's sign or is zero, so no part is negative when every share is non-negative. A fixed amount is never recomputed from rounded parts, and every other total is the sum of amounts already rounded (F-9);
- a batch, and every message that holds several amounts, carries one currency, and a batch with a second currency is refused at release (F-10).

A future contract that carries money, such as a claim, remittance or payment contract, SHALL meet this requirement and state that it uses the rounding rule of F-9.

#### Scenario: An amount is sent as a number
- **WHEN** a message carries an amount as the JSON number 123.45
- **THEN** it fails the canonical schema

#### Scenario: A batch holds two currencies
- **WHEN** an assembled batch holds lines in USD and in another currency
- **THEN** release is refused and the lines are named

#### Scenario: An estimate has a fraction of a cent
- **WHEN** openPractice computes an estimated amount of 33.335 USD
- **THEN** it rounds it once, half away from zero, in exact decimal arithmetic, to 33.34, stores 3334 cents, and adds the rounded amount into every total; it never uses binary floating point, in which a half cent can land on either side of the midpoint

#### Scenario: A fixed amount is split three ways
- **WHEN** a deposit of 100.00 USD is split into three equal parts
- **THEN** taking the parts in their stated order, or in ascending order of their identifiers where none is stated, the running totals round to 33.33, 66.67 and 100.00, so the first and third parts are 33.33 and the second is 33.34; they add to 100.00, each is within a cent of its exact share, and the deposit is still 100.00 against its bank line

#### Scenario: An amount is split two ways at a half cent
- **WHEN** 5.35 USD is split equally between two parts, with no order stated
- **THEN** taking the parts in ascending order of their identifiers and computing in exact decimal, the running totals are 2.675, rounded half away from zero to 2.68, and 5.35, so the first part is 2.68 and the second 2.67

#### Scenario: A small fee is split many ways
- **WHEN** a processor fee of 1.10 USD is allocated equally across the 31 receipts of a settlement
- **THEN** taking the receipts in ascending order of their identifiers, the 1st, 3rd, 5th, 7th, 9th, 11th, 12th, 14th, 16th, 18th, 20th, 21st, 23rd, 25th, 27th, 29th and 31st get 0.04 and the other 14 get 0.03; they add to 1.10, and no receipt gets a negative share

#### Scenario: A batch in another currency
- **WHEN** a 1.0 batch or bank-lines result carries a currency other than USD
- **THEN** it fails the canonical schema, and release or reading is refused
