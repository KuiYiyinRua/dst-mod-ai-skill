# Persistence and compatibility

## Save only durable state

Implement `OnSave`/`OnLoad` or component save methods for state that must survive reload. Save simple, versionable data rather than entity internals, task handles, callbacks, or derived caches. Restore references through supported entity-reference mechanisms when needed.

Treat loading as hostile input: fields may be absent, old, malformed, or from a newer configuration. Supply defaults, clamp ranges, migrate old keys, and make migrations idempotent. Recompute derived state after loading.

Temporary combat/action state normally should not survive a save. On load, clear temporary tags, task references, overrides, busy flags, and transient invulnerability before reconstructing durable state.

## Handle entity lifecycle

Consider death, resurrection, despawn, migration, owner disconnect, inventory transfer, container removal, world rollback, and prefab removal mid-task. Never retain an entity without checking `IsValid()` at delayed execution time. Avoid closures that unnecessarily keep removed entities alive.

## Support shards and caves

Do not assume one world process. Decide whether state is entity-local, shard-local, or cluster-wide. Test surface/caves migration when player state, world state, cooldowns, or singleton managers are involved. Use supported shard communication for cross-shard data; ordinary fields, events, and netvars do not create cluster-wide storage.

Run world-spawning logic only on the intended shard and master simulation. Guard singleton creation against duplicate spawns after rollback or migration.

## Preserve compatibility

- Namespace all identifiers.
- Patch narrowly and compose with existing callbacks.
- Avoid mutating shared Klei tables in place unless no supported hook exists; copy before specialization.
- Feature-detect optional mod APIs and components.
- Do not depend on another mod's load order unless explicitly declared and guarded.
- Keep old save/config keys readable when renaming public identifiers.

## Player tags

Audit temporary tags during save/load changes. Every temporary tag must have a symmetric removal path and load-time cleanup if an interrupted state could persist. When both a tag and field represent one state, clear or restore them together. Keep permanent capability tags out of migration cleanup.
