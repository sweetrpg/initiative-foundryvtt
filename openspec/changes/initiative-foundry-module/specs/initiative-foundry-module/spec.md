## ADDED Requirements

### Requirement: GM-only operation
Only the active GM's client SHALL connect to Initiative and perform sync. Player clients and
non-active GM clients SHALL NOT hold the credential or send writes.

#### Scenario: Player joins
- **WHEN** a player connects to a world with the module enabled
- **THEN** the player's client makes no requests to Initiative

#### Scenario: Active GM changes
- **WHEN** the active GM role moves to another GM client
- **THEN** the new client resumes from the encounter's current revision and the old one stops

### Requirement: Credential handling
The Initiative integration credential SHALL be stored as a client-scoped setting on the GM's
machine, never as a world-scoped setting, and SHALL NOT be logged or shown after entry.

#### Scenario: Another user inspects settings
- **WHEN** a player or other GM inspects world settings
- **THEN** the credential is not present

### Requirement: Link a combat to an encounter
The GM SHALL be able to link the current Foundry `Combat` to an existing Initiative encounter or
create a new one from it. Links SHALL be recorded as `external_refs` on Initiative combatants
(`foundry.actor`, `foundry.combatant`), not in a module-side store.

#### Scenario: Create from combat
- **WHEN** the GM creates an Initiative encounter from the current combat
- **THEN** each Foundry combatant becomes an Initiative combatant carrying its Foundry references

#### Scenario: Relink after reload
- **WHEN** the GM reloads Foundry and reopens the linked combat
- **THEN** the module finds the combatants by reference lookup and resumes sync

### Requirement: Foundry changes flow to Initiative
The module SHALL send combatant additions and removals, initiative values, turn and round
changes, and mapped health changes from the linked combat to the API.

#### Scenario: Roll initiative in Foundry
- **WHEN** a combatant's initiative is rolled in Foundry
- **THEN** the module sends the value to Initiative and renders the resulting `turn_queue`

### Requirement: Initiative changes flow to Foundry
The module SHALL follow the API event stream for the linked encounter and apply changes to the
Foundry combat and mapped actor data, ignoring events originated by its own credential.

#### Scenario: Edit in another Initiative client
- **WHEN** a user changes a combatant's health in the Initiative web app
- **THEN** the linked actor's mapped attribute updates in Foundry

#### Scenario: No echo
- **WHEN** the module applies an Initiative change to Foundry
- **THEN** it does not send that change back to Initiative

### Requirement: Initiative decides order and validity
The module SHALL NOT compute initiative order, validate health values against game-system rules,
or evaluate entitlements. It SHALL render the API's `turn_queue` and surface the API's typed
errors.

#### Scenario: Value out of range
- **WHEN** the API rejects a health value as out of range
- **THEN** the module reverts the Foundry value and shows the error naming the field

### Requirement: Conflict handling
On a 409 revision conflict the module SHALL adopt the returned encounter state in Foundry and
notify the GM that their change was not applied.

#### Scenario: Stale write
- **WHEN** a write returns 409
- **THEN** the Foundry combat reflects the returned state and the GM sees a notice

### Requirement: Health mapping by system alias
The module SHALL map Initiative health fields to Foundry actor data paths using a table keyed by
the API's game-system alias. For a Foundry system with no mapping, it SHALL sync initiative and
turn state only and tell the GM that health sync is unavailable.

#### Scenario: Unmapped system
- **WHEN** the world's Foundry system has no mapping
- **THEN** initiative and turns sync and the GM is told health is not synced

### Requirement: Connectivity and failure behavior
While the API is unreachable the module SHALL show a status indicator, keep Foundry usable, queue
no writes, and on reconnect refetch the encounter before resuming sync. Entitlement and
authentication errors SHALL be shown with the key or reason from the API.

#### Scenario: Connection drops
- **WHEN** the event stream disconnects
- **THEN** the module shows it is offline, reconnects with backoff, and refetches before syncing

#### Scenario: Integrations not entitled
- **WHEN** the API returns the integrations entitlement error
- **THEN** the module explains that the account's plan does not include integrations

### Requirement: Localization
All user-facing strings SHALL come from Foundry language files with English as the fallback.

#### Scenario: Missing translation
- **WHEN** a string has no translation for the user's language
- **THEN** the English text is shown

### Requirement: Packaging
Releases SHALL publish a manifest and zip suitable for Foundry's package installer, with
accurate `minimum`, `verified`, and `maximum` compatibility values.

#### Scenario: Install from manifest
- **WHEN** a GM installs the module from the release manifest URL
- **THEN** Foundry installs it and lists its declared compatibility
