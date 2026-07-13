# Gameplay systems

## Build characters and player extensions

Use the established character prefab pattern and player-common helpers. Add gameplay components only on the master simulation; expose client-readable behavior through replicas, classified entities, or netvars as appropriate. Keep character stat changes keyed so they can be removed cleanly.

Handle human/ghost, death, resurrection, migration, load, and new-spawn paths. Reset temporary movement, combat, animation, and control state on every transition. Do not assume `ThePlayer` is the actor: code that supports remote players must use its explicit `inst`/`player` argument.

## Create items, equipment, and containers

Choose the engine components that own behavior (`inventoryitem`, `equippable`, `weapon`, `armor`, `finiteuses`, `fueled`, `perishable`, `container`, etc.) instead of duplicating their state in Lua fields. Register inventory atlas images and equip symbols consistently.

For equipment, restore all modifiers and visual overrides in unequip, item removal, owner death, and load recovery paths. For containers, validate openers and item transfers on the server; configure slots, filters, widget setup, and replica behavior together.

## Add recipes and crafting

Use the current project/API recipe-registration pattern. Keep product prefab names, ingredient prefabs, atlas images, filters/tabs, builder tags, and unlock conditions consistent. Ensure recipe actions are usable on clients while final crafting validation and consumption remain authoritative.

When changing an existing recipe, preserve save compatibility for existing items and avoid relying on recipe display state as an authorization check.

## Implement buffs and combat effects

Represent a buff with an appropriate component/debuff/prefab lifecycle instead of only tags. Define application, refresh/stack rules, expiration, removal, death behavior, save/load behavior, and network/UI projection. Avoid a periodic task per entity when an existing timer/debuff mechanism fits.

For combat, validate attacker, target, distance, targetability, PvP mode, immunity, and damage source on the server. Use combat hooks narrowly; avoid globally changing shared damage tables. Make cooldowns and costs server-owned, and make cosmetic hit feedback safe to duplicate or miss.

## Spawn loot and world content

Make drops server-authoritative and idempotent across death/removal callbacks. Clamp probabilities and quantities; account for stacks, inventory overflow, no-drop tags, and rollback. Scope world spawning by shard and avoid world-wide scans in hot paths.

## Review matrix

For each gameplay system, test spawning, pickup/equip/use, stacking/refreshing, save/reload, death/respawn, migration, disconnect, rollback, host, remote client, and dedicated server as relevant.
