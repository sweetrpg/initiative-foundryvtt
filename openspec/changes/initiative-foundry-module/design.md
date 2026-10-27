## Context

Foundry modules run as JavaScript inside each connected browser. The GM's browser is the only
client that should talk to Initiative; player browsers must not hold the credential. Foundry's
current stable generation is 14 (14.369 as of October 2026). Package metadata (`minimum`,
`verified`, `maximum`) is set in the manifest; check current generations before release.

## Goals / Non-Goals

**Goals:**
- Foundry combat and an Initiative encounter stay in step for a GM.
- Zero API changes: everything uses the public API from `initiative-integration-access`.
- Fail visibly and recover, without corrupting either side.

**Non-Goals:**
- Players using Initiative through Foundry.
- Reimplementing ordering, validation, or entitlement logic in the module.
- Offline use. Foundry itself needs connectivity here, so the module does too.

## Decisions

**Only the active GM's client syncs.** Foundry has no server-side module hook for user modules, so
the module elects the active GM (`game.users.activeGM`) to own the connection. Other GM clients
and players run no sync. This avoids duplicate writes and keeps the credential on one machine.

**Credential storage.** Stored as a client-scoped setting in the GM's browser, never a world
setting, since world settings are readable by other users. Entered once through the module's
settings dialog; never logged.

**Sync model.** Foundry changes (combatant added or removed, initiative rolled, turn or round
changed, tracked health attribute changed) are sent to the API. API events are applied to Foundry
`Combat` and actors. Each write carries the encounter `revision`; on 409 the module adopts the
returned state and re-applies Foundry's pending change only if it still makes sense.

**Initiative decides order.** The module sends initiative values and renders the `turn_queue` the
API returns into Foundry's combat order. Foundry's own sort is overridden for the linked combat.
If this proves too intrusive, a one-way mode (Foundry to Initiative only) is the fallback.

**Origin tagging prevents loops.** The module ignores events whose origin is its own credential.
Changes the module applies to Foundry are flagged so its own hooks do not send them back.

**Health mapping lives in the module.** Initiative field keys come from the API's game-system
definition; Foundry actor data paths differ per Foundry game system (e.g. `dnd5e`). The module
holds a small mapping table per supported Foundry system, keyed by the system alias the API
returns. Unmapped systems sync initiative and turn only. This is the one place the module knows
a game system by name; it is data, not logic about rules.

**Linking.** Combatants carry `foundry.actor` and `foundry.combatant` in `external_refs`. The
module finds existing combatants by reference lookup instead of keeping its own table.

## Risks / Trade-offs

[Foundry updates can break hooks or data paths] → Pin `minimum`/`verified` honestly, keep the
mapping table small, and test against the current stable generation each release.

[Two-way sync can ping-pong or conflict] → Revision checks, origin tagging, and adopt-server-state
on 409.

[Active GM changes mid-session] → The new active GM resumes from the current API revision; the
previous holder stops on losing the role.

[Overriding Foundry's combat order surprises users] → Explicit setting, off by default until the
GM links an encounter.

## Migration Plan

New module, no migration. Ship as a GitHub release with a manifest URL; Foundry installs from the
manifest.

## Open Questions

- Which Foundry systems get health mappings first (likely `dnd5e`)?
- Minimum supported Foundry generation (13 or 14 only)?
- Is one-way mode needed at launch?
