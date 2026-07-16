# World generation and world settings

## Separate generation from runtime

Put generation-time registration in `modworldgenmain.lua`. It runs in a restricted environment and can also be loaded for frontend world-creation data. Do not access `TheWorld`, spawned entities, runtime components, `ThePlayer`, or main-only hooks there.

Use `modservercreationmain.lua` only when the mod needs server-creation/frontend definitions that do not belong in runtime code. Confirm the actual loader and available environment in official `mods.lua` and `modutil.lua`.

## Extend official generation data

Use the mod-aware helpers exposed by the environment for rooms, tasks, task sets, start locations, levels/settings presets, tiles, and related definitions. Namespace every identifier. Start from the closest official room/task/preset and copy only the fields that must differ.

Keep these layers consistent:

- A room defines contents and distribution.
- A task arranges rooms and locks/keys.
- A task set selects and orders tasks.
- A level/preset chooses task sets and world settings.
- Tile registration includes render, minimap, turf, and property data.

Do not mutate an official shared definition in place when a copied definition or post-init is available.

## Make generation deterministic and compatible

Use the generation RNG/context expected by official code; avoid time, filesystem order, client state, or unsynchronized global randomness. Stable generation inputs must produce stable results across master and caves.

Validate required prefabs and assets before adding them to generation tables. Avoid impossible lock/key graphs, missing mandatory rooms, unbounded counts, or density values that can explode generation time. Preserve compatibility with existing presets and other worldgen mods by adding narrowly scoped definitions.

## Handle runtime world spawning separately

If content should appear after generation, implement server-owned runtime spawning in `modmain.lua`, a world component, or a world prefab. Guard master/cave shard ownership, save spawned identity or use a manager, and make rollback/load idempotent.

Do not confuse generation topology with runtime map edits. Runtime tile changes, regrowth, and world events need persistence, networking, and map rebuild considerations.

## Verify worldgen

Create new surface and cave worlds, regenerate, load old worlds, and test several presets/seeds. Inspect worldgen logs for missing rooms/tasks/prefabs and excessive generation time. A successful existing-world load does not validate `modworldgenmain.lua`.
