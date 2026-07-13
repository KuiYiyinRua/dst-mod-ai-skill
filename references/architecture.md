# Architecture and lifecycle

## Place code deliberately

- Keep `modmain.lua` as composition: configuration, assets, prefab lists, hooks, registrations, and module loading.
- Put reusable authoritative behavior in components or focused modules.
- Put client-readable projections in replicas.
- Put entity construction in `scripts/prefabs/` and visual interface code in widgets/screens.
- Extend stategraphs and actions through supported registration hooks; avoid replacing global tables when a scoped hook works.

## Prefer scoped hooks

Use `AddPrefabPostInit`, `AddComponentPostInit`, `AddPlayerPostInit`, `AddClassPostConstruct`, component-action hooks, and stategraph hooks at the narrowest applicable scope. Make hooks idempotent where reload or multiple integrations could repeat them. Preserve original callbacks when wrapping and maintain argument/return semantics.

## Construct prefabs safely

Declare required `Assets` and dependent prefabs. Build common entities before `SetPristine`; add server components afterward. Use `MakeInventoryPhysics`, `MakeObstaclePhysics`, haunt helpers, burnable helpers, and related Klei helpers when their behavior matches the entity.

Register the prefab through `PrefabFiles` or return it from an imported prefab module as the project expects. Namespace prefab and event names to avoid collisions.

## Components and cleanup

Define clear ownership for tasks and listeners. Store handles that must be cancelled. Cleanup must tolerate repeated calls and partially removed entities.

Pair these operations:

- `ListenForEvent` with `RemoveEventCallback`
- `DoTaskInTime`/`DoPeriodicTask` with `Cancel`
- `AddTag` with `RemoveTag` for temporary tags
- external speed/damage multipliers with keyed removal
- `OverrideSymbol` with `ClearOverrideSymbol`
- control/brain/state restrictions with restoration

Prefer fields or components for private state. Use tags only when engine queries, action discovery, or replication semantics genuinely require tags. Never generate unbounded dynamic tag names.

## Actions and stategraphs

Keep action discovery cheap and side-effect free. Validate the action again when executed. Define consistent `strfn`, range, priority, mount/ghost rules, and component-action registration.

For stategraph work, specify tags, timeline, timeout, `onenter`, `onexit`, interruption cleanup, and buffered-action handling. Clear temporary collision, visibility, invincibility, locomotion, and symbol state from every exit path.

## Configuration

Read mod configuration once into a namespaced configuration/module layer when practical. Validate defaults and types. Avoid scattering `GetModConfigData` calls through hot paths. Keep configuration identifiers stable across versions.
