## Why

Game masters running Foundry VTT want Initiative! to follow and drive their Foundry combat without
retyping combatants. `initiative-platform` (platform repo, `openspec/changes/initiative-platform`)
built the API with realtime events, integration credentials, `external_refs`, and game-system
aliases so this module needs no API changes. This change builds the module itself.

## What Changes

- A Foundry VTT module (JavaScript, no build step beyond bundling if needed) installed per world.
- GM-only setup: the GM pastes an Initiative integration credential and picks which Initiative
  encounter to link, or creates one from the current Foundry combat.
- Two-way sync between a Foundry `Combat` and an Initiative `Encounter`: combatants, initiative
  values, turn and round, and health values, using the public API and event stream.
- Links are stored as `external_refs` (`foundry.actor`, `foundry.combatant`) on Initiative
  combatants, so re-linking and lookups need no module-side database.
- Initiative remains the place where ordering, validation, and entitlements are decided. The
  module displays and forwards.
- Supported systems at launch (Foundry IDs): `dnd5e`, `pf2e`, `cyphersystem`, `demonlord`,
  `weirdwizard`, `t2k4e`, `genesys`. Minimum Foundry generation: 14.
- Multi-user: the GM can place the encounter in an Initiative session and link Foundry users to
  session members so linked accounts are notified on their turn in Initiative clients. Foundry
  players keep using Foundry and hold no credentials.
- Build order: after `initiative-api` and `initiative-desktop`, in either order with `initiative-web`.
- Out of scope: Initiative-style player views inside Foundry (Foundry's own UI serves players), any
  Foundry-specific API endpoint, hosting a Foundry instance, support for non-GM users to configure
  the link, inviting or approving session members from Foundry.

## Capabilities

### New Capabilities

- `initiative-foundry-module`: module configuration, credential handling, encounter linking, sync
  rules, failure behavior, and packaging.

### Modified Capabilities

(none)

## Impact

- **Code**: new module in `frontends/initiative-foundryvtt`.
- **Depends on**: `initiative-api` endpoints for encounters, combatants, `external_refs` lookup,
  game-system aliases, event stream, and integration credentials; the integrations entitlement
  key in the entitlements catalog.
- **Platform**: none beyond the submodule; releases via `repo-setup-standard`.
