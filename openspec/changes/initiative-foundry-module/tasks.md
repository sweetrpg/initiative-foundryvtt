## 1. Setup

- [ ] 1.1 Run `repo-setup-standard` (CI, branch protection, `develop` branch, release workflow, community docs)
- [ ] 1.2 Set manifest `minimum` 14 and `verified` to the generation tested; leave `maximum` unset
- [ ] 1.3 Module skeleton: manifest, language file, settings registration, lint and test setup

## 2. Connection

- [ ] 2.1 Active-GM election and handoff
- [ ] 2.2 Credential setting (client scope), API client with typed errors, revision handling
- [ ] 2.3 Event stream client: reconnect, catch-up, gap refetch, status indicator

## 3. Sync

- [ ] 3.1 Link or create an encounter from the current combat; `external_refs` writes and lookup
- [ ] 3.2 Foundry to Initiative: combatant add/remove, initiative, turn and round
- [ ] 3.3 Initiative to Foundry: apply `turn_queue` and turn state, origin-tag filtering, no echo
- [ ] 3.4 Health mapping tables for `dnd5e`, `pf2e`, `cyphersystem`, `demonlord`, `weirdwizard`, `t2k4e`, `genesys`; unmapped fallback
- [ ] 3.5 409 handling and error display, including entitlement errors

## 4. Sessions

- [ ] 4.1 Session picker when creating or linking an encounter
- [ ] 4.2 Foundry user to session member linking UI; set `controller_user_ids` from actor ownership
- [ ] 4.3 Refuse links to non-members with a clear message

## 5. Release

- [ ] 5.1 Manifest and zip release workflow
- [ ] 5.2 Tests for sync rules, loop prevention, handoff, and failure behavior against a recorded API fixture
- [ ] 5.3 Manual test against a live Foundry world on the current stable generation
- [ ] 5.4 README with setup steps, no secrets in docs
