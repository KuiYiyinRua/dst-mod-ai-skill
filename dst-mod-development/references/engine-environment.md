# Script environments and engine access

## Identify the executing environment

Do not apply one universal rule for `GLOBAL`. `modmain.lua`, prefab modules, widgets, screens, stategraphs, and imported modules can be loaded with different environments. First inspect nearby working code and the loader (`modimport`, `require`, prefab loader, frontend loader, or stategraph loader).

Use symbols supplied by that environment directly. Access a runtime object through `GLOBAL`/`_G` only when the environment requires it, and bind it locally only when that is already the project convention. Do not add `local GLOBAL = GLOBAL or _G` merely to quiet a linter; it can alter strict-environment behavior.

Avoid leaking new globals. Export a deliberate, namespaced API only when modules genuinely need a shared interface, and document its owner and load order.

## Use engine objects by side and lifetime

- `TheWorld`: world state, components, map, and master-simulation checks.
- `ThePlayer`: local client player only; never use it as a server-side substitute for the RPC sender or target.
- `AllPlayers`: current player collection; do not assume it is complete/valid during teardown.
- `TheNet`: networking/session information; guard dedicated-client-only behavior.
- `TheSim`: engine queries, coordinate conversion, and entity search; bound search radius and frequency.
- `TheFrontEnd`, cameras, HUD, input: frontend/client presentation only.

Check entity validity before delayed use. Check component/replica existence before accessing it on optional prefab variants or the opposite simulation side.

## Query the world safely

Use the smallest search radius, required/excluded tags, and an additional predicate when calling `FindEntities`/`FindEntity`. Never search all entities each frame. Use distance-squared checks where possible. Treat every query result as possibly removed or migrated before acting.

When changing tiles, paths, map, or world state, verify master-simulation and shard ownership. Prefer world events/state watchers for state transitions over high-frequency polling.

## Physics and movement

Use established physics helpers and collision groups. Restore collisions, motor velocity, platform state, and locomotor modifiers on all exits. Teleport only after validating passability, water/platform rules, target validity, and server authority. Do not let a client select an unvalidated teleport destination.

## Find authoritative API evidence

Search the narrowest relevant source directory first: target module, existing call sites, then the installed game's `scripts/` tree. Read definitions and call sites before relying on a signature. Keep searches bounded by file type and directory; do not recursively scan animation/export trees to find a Lua symbol.
