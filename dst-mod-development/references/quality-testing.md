# Quality, investigation, and testing

## Investigate before editing

Use fast, bounded searches. Start with `rg` for identifiers, prefab names, events, RPC namespaces, and analogous Klei code. Narrow large searches by directory and extension. Avoid unbounded searches through binary asset trees.

When game scripts are available locally, verify uncertain APIs there. Check call sites as well as definitions because lifecycle and execution-side assumptions often live at the call site. If scripts are unavailable, label recalled APIs as assumptions and ask for runtime evidence when necessary.

## Write maintainable Lua

- Follow the repository's Lua version and style; DST commonly requires Lua 5.1-compatible syntax.
- Prefer locals and namespaced modules; do not leak accidental globals.
- Use ASCII quotes and preserve UTF-8 text.
- Keep hot callbacks allocation-light and avoid full-world scans in periodic tasks.
- Check numeric inputs for finite values before math or RPC use.
- Use stable keyed modifiers instead of directly overwriting shared multipliers.
- Comment execution-side, lifecycle, or compatibility reasoning—not obvious syntax.

Do not apply a universal `GLOBAL` rule. `modmain.lua`, prefab modules, widgets, and imported modules can receive different environments. Follow working neighboring code and verify how the file is loaded. Avoid lint-only rewrites that could change runtime lookup behavior.

## Debug without destabilizing the server

Prefix logs with a stable mod/system identifier and log state transitions or failures, not every update tick. Gate verbose diagnostics behind a configuration/debug flag. Never print authentication data, private player data, full serialized saves, or unbounded tables.

Use `pcall` only at optional integration or external-data boundaries where recovery is meaningful. Do not wrap core gameplay broadly and silently discard errors; preserve a useful traceback/log and keep state atomic. Remove temporary console commands and debug hooks before release, or protect admin-only commands with server-side permission checks.

## Enforce performance budgets

Treat `AddPrefabPostInitAny`, component-action collectors, brain nodes, stategraph timelines, `DoPeriodicTask`, world-state watchers, and UI update loops as hot paths. Filter by tags/prefab/component as early as possible. Cache immutable lookups, but invalidate caches whose entity/config inputs can change.

Prefer events, timers, sleep/wake, and component-owned updates over per-frame polling. Bound entity searches by radius, required/excluded tags, frequency, and result count. Test worst-case player/entity counts and repeated mass spawning; watch both server tick rate and client rendering cost.

## Validate proportionally

Run repository-provided checks first. If available, use a Lua parser/linter compatible with the project's Lua dialect. Review the diff and search for stale renamed identifiers.

In-game validation matrix:

1. Host/local player.
2. Remote client.
3. Dedicated server without rendering assumptions.
4. Save, reload, rollback, death/respawn, and reconnect.
5. Surface/caves migration when relevant.
6. High latency or repeated input for RPC/action work.
7. Mod enabled beside likely integration points.
8. Entity sleep/wake, inventory limbo, and long update when the feature owns background work.
9. World creation/regeneration when worldgen, tiles, presets, or spawn rules change.
10. Keyboard/mouse and controller paths when input or UI changes.

Inspect both server and client logs. A clean host test does not prove remote-client correctness.

## Final review

- Authority and execution side are explicit.
- RPC inputs are validated and rate-limited where needed.
- Netvars exist before `SetPristine` and consumers initialize current values.
- Listeners, tasks, tags, modifiers, and overrides have cleanup.
- Save data is version-tolerant and transient state is not persisted accidentally.
- Asset/prefab registration is complete.
- No unrelated user changes were overwritten.
- The handoff lists checks run and remaining in-game tests.
