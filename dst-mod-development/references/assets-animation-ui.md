# Assets, animation, FX, and UI

## Register assets

Register each asset with the correct type and exact case-sensitive path. Keep atlas XML element names consistent with referenced texture names. When an inventory image uses a custom atlas, register and reference both atlas and image consistently.

Confirm that each prefab file is loaded, dependent prefab is declared where appropriate, and build/bank names match compiled animation archives. Do not infer these names solely from filenames; inspect established usages or source project files.

## Build visual prefabs

For network-visible FX, construct the network entity correctly and decide whether the effect is server-spawned and replicated or locally spawned on each client. Cosmetic local FX must not mutate gameplay. Server FX should remove itself deterministically and remain safe on dedicated servers.

Set bank, build, animation, orientation, layer, sort order, scale, bloom/light, and symbol overrides explicitly when the effect depends on them. Avoid periodic tasks for one animation when `animover` or a timeline callback is sufficient. Remove event callbacks and tasks on teardown.

## Work with stategraph animation

Verify animation names against the build. Handle missing optional symbols/animations gracefully when supporting skins or other mods. Restore hidden symbols, overridden builds, colors, scale, and visibility on interruption as well as normal completion.

## Build UI safely

Keep screens/widgets client-side and presentation-focused. Separate layout from data access. Subscribe to dirty events or replica state and remove listeners when destroyed. Support different resolutions and HUD scales; anchor relative to existing UI rather than fixed screen pixels when possible.

Input handlers must respect chat, console, controller, focus, pause, and screen ownership. Send validated intent to the server instead of performing gameplay mutations from the UI.

## Asset checklist

- Path, extension, and filename case match.
- `IMAGE` and `ATLAS` pairs exist where required.
- `ANIM`, `SOUNDPACKAGE`, and `SOUND` declarations match actual usage.
- Dedicated servers do not execute rendering-only assumptions.
- Missing skins or optional assets fail gracefully.
- Packaging includes every referenced file and excludes raw working files unless intentionally distributed.
