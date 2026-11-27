## ADDED Requirements

### Requirement: PO-01 Set up one office and one legal entity in openPractice

openPractice SHALL provide a guided setup in which an authorized practice administrator registers one primary office and one legal operating entity. Setup SHALL capture the office name and address and the legal entity name. It SHALL ask whether the practice has a single practitioner or is a group and save that choice with the practice. Both choices SHALL use the same one-office, one-entity model.

#### Scenario: Set up a single-practitioner practice

- **WHEN** an administrator enters the office and legal entity details and selects Single practitioner
- **THEN** openPractice saves that selection with one office and one legal entity

#### Scenario: Set up a group practice

- **WHEN** an administrator enters the office and legal entity details and selects Group
- **THEN** openPractice saves that selection with one office and one legal entity
- **AND** the openHR setup uses one operating Company for that entity

### Requirement: PO-02 Create or link the openHR Company from openPractice

openPractice SHALL let the administrator choose to create a Company or select an existing Company for the legal entity on the configured openHR site. The connector SHALL perform the chosen operation and verify the result on that site. openPractice SHALL record the verified site and Company identifier against the legal entity and show the administrator whether the Company is connected. Linking an existing Company SHALL leave that Company unchanged.

#### Scenario: Create a Company for a new legal entity

- **GIVEN** the legal entity has no Company link and the administrator chooses Create Company on the configured openHR site
- **WHEN** the connector completes and verifies creation
- **THEN** the site has one new operating Company for the legal entity
- **AND** openPractice saves the new Company identifier and shows the Company as connected

#### Scenario: Link an existing Company

- **GIVEN** the administrator selects an existing Company on the configured openHR site and confirms that it represents the practice's legal entity
- **WHEN** the connector verifies the selection
- **THEN** openPractice saves the site and the selected Company identifier and shows the Company as connected
- **AND** openHR has the same Company records as before the link

### Requirement: PO-03 Retry onboarding without creating duplicate Companies

Before the first create attempt, the connector SHALL persist a stable request identity for the legal entity and target openHR site. Repeated or concurrent submissions of the same create SHALL resolve to the same Company. After an uncertain outcome, the connector SHALL reconcile with openHR using the stable identity before attempting another create. Company names alone SHALL NOT establish a match. If the Company cannot be verified, openPractice SHALL retain the office and entity details and show the unresolved connection so the administrator can retry without re-entering those details.

#### Scenario: Retry after openHR creates the Company and the response is lost

- **GIVEN** openHR creates the Company for the request but openPractice does not receive the response
- **WHEN** the administrator retries the same onboarding request
- **THEN** the connector verifies the Company already created and openPractice saves the original Company identifier
- **AND** openHR has exactly one Company for that request

#### Scenario: Two submissions of the same create overlap

- **WHEN** the connector receives concurrent create submissions for the same legal entity and openHR site
- **THEN** the submissions resolve to the same Company identifier
- **AND** openHR has exactly one Company for that request

#### Scenario: Reconciliation cannot verify a Company

- **GIVEN** the first attempt has an uncertain outcome and the connector cannot establish whether openHR created the Company
- **WHEN** the administrator retries the same onboarding request
- **THEN** openPractice retains the saved setup and shows the connection as unresolved
- **AND** the connector does not issue a second create while the outcome is unresolved
