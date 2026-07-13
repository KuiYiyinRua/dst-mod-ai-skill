---
name: dst-mod-development
description: Develop, debug, review, and refactor Don't Starve Together (DST) mods in Lua, including modmain/modinfo, prefabs, components and replicas, stategraphs, actions, UI/widgets, assets, save/load, RPCs, net variables, caves and dedicated-server compatibility. Use whenever an agent changes or diagnoses DST mod code and must reason about master simulation versus clients, networking, persistence, lifecycle cleanup, API usage, or multiplayer testing.
---

# Develop DST Mods

Apply this workflow before editing code.

1. Inspect repository instructions, nearby code, `modinfo.lua`, `modmain.lua`, and relevant project documentation. Treat project conventions as authoritative unless they conflict with the user request or verified engine behavior.
2. Classify every affected path as frontend-only, client simulation, master simulation, or shared prefab initialization. State which side owns each mutation.
3. Locate the local DST official-script source before relying on a remembered API. Check the project-provided source path first, then `template/scripts` under the project root, `../template/scripts` beside the mod directory, and the installed game's `data/databundles/scripts.zip`. Read the analogous Klei implementation and at least one call site when available; otherwise state that the source was unavailable.
4. Make the smallest coherent change. Preserve unrelated user edits and existing public interfaces.
5. Review networking, lifecycle cleanup, save/load, shard behavior, and asset registration as applicable.
6. Run available syntax/static checks and focused tests. Report any behavior that still requires in-game verification.

## Route references

Read only the references relevant to the task:

- Networking, prediction, RPC, net variables, replicas, and UI synchronization: [networking.md](references/networking.md)
- Prefabs, components, hooks, actions, stategraphs, and lifecycle: [architecture.md](references/architecture.md)
- Save/load, cleanup, caves/shards, and compatibility: [persistence-compatibility.md](references/persistence-compatibility.md)
- Animations, FX, atlases, inventory images, and asset registration: [assets-animation-ui.md](references/assets-animation-ui.md)
- Particle VFX, envelopes, emitters, dedicated-server behavior, and visual cleanup: [particles-fx.md](references/particles-fx.md)
- `modinfo.lua`, configuration options, localization, mod dependencies, and release packaging: [modpackaging.md](references/modpackaging.md)
- Characters, items, equipment, recipes, containers, buffs, combat, and loot: [gameplay-systems.md](references/gameplay-systems.md)
- Script environments, `GLOBAL`, engine objects, world queries, physics, and official-source discovery: [engine-environment.md](references/engine-environment.md)
- Code quality, investigation, validation, and delivery checklist: [quality-testing.md](references/quality-testing.md)

## Enforce core invariants

- Keep authoritative gameplay state and mutations on the master simulation. Never trust client-supplied targets, positions, quantities, permissions, or cooldown state without server validation.
- Create networked entities and net variables before `SetPristine`; place server-only components and logic after the non-master early return.
- Use RPCs for client intent, net variables/replicas for replicated state, and local events only for same-simulation communication. Do not assume plain Lua fields or `PushEvent` cross the network.
- Keep UI presentational. Read replicated state and send intent; do not make widgets authoritative or responsible for persistence.
- Pair every listener, task, override, tag, speed multiplier, symbol override, and temporary state with idempotent cleanup.
- Namespace prefab names, RPC namespaces, netvar/debug names, tags, actions, globals, tuning keys, and save keys.
- Do not invent an API or signature. Confirm it in project or game scripts when uncertainty could affect correctness.
- Do not edit generated binary assets merely to silence source-level problems. Preserve established encoding, line endings, and formatting.

## Deliver

Summarize the behavior changed, execution side, files touched, and verification performed. Call out dedicated-server, caves, reconnect, rollback, and multiplayer risks when relevant. Distinguish verified facts from assumptions.
