# Mod metadata, configuration, localization, and packaging

## Maintain `modinfo.lua`

Keep metadata truthful and stable: name, description, author, version, `api_version`, DST compatibility, server/client requirements, priority, game modes, and icon references. Test the intended mode explicitly:

- Set `client_only_mod = true` only when the mod changes local presentation/input and never authoritative gameplay.
- Set `all_clients_require_mod = true` when clients need the mod's prefabs, actions, assets, RPC tables, or replicated code.
- Use a server-only/network-compatible configuration only when unmodified clients can safely connect and interpret every replicated entity and action.

Do not advertise compatibility that the network model does not support. Check current official examples before adding legacy flags.

Define configuration through `configuration_options`. Give each option a stable internal name, an explicit default, a finite options list, and localized labels/hover text. Treat configuration as input: validate types and ranges before use. Document whether a value is read at world start, player spawn, or dynamically.

Read configuration through `GetModConfigData` in a controlled initialization/configuration module. Do not assume every peer has the same local configuration; authoritative gameplay configuration belongs on the server.

## Localize correctly

Register strings in the expected `STRINGS` tables for prefab names, recipe names, actions, character speech, and UI. Use the project's locale-file convention and retain a fallback locale. Keep user-facing text separate from gameplay identifiers.

Use `STRINGS.NAMES`/`STRINGS.RECIPE_DESC` and character speech tables only where their expected consumers use them. Do not mutate unrelated global string tables wholesale. Verify both English fallback and target locales with missing/old translations.

## Declare and load modules

Keep `Assets` and `PrefabFiles` complete and deterministic. Load independent modules in a predictable order; avoid a module relying on an incidental global created by another. Use `modimport`/`require` according to the project's existing loading convention and understand whether the module executes in modmain, prefab, or frontend context.

Use the entry file that matches the execution environment:

- `modmain.lua` for runtime simulation/frontend registration.
- `modworldgenmain.lua` for generation-time rooms, tasks, levels, tiles, and presets.
- `modservercreationmain.lua` only for server-creation/frontend world-option setup when required.

Remember that worldgen exposes a smaller API surface; official `modutil.lua` explicitly separates APIs available in worldgen from main-only hooks.

Declare optional mod integrations defensively. Check availability before importing or calling an external API, and provide a harmless no-integration path.

## Package for release

Include scripts, prefabs, animations, atlas/XML/texture pairs, sounds, localization, and every file referenced at runtime. Exclude source exports, editor backups, logs, test captures, credentials, and development-only tooling unless deliberately distributed.

Understand `forcemanifest`: Workshop mods normally use manifests, while local mods may not. Do not enable or disable it to mask missing asset declarations. Before publishing, launch the packaged copy rather than only the source tree. Verify Workshop metadata, icon paths, configuration screen, world-creation screen, dedicated-server startup, and a remote-client join.
