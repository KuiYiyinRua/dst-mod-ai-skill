# Frontend, input, audio, skins, and commands

## Distinguish frontend and in-game UI

Frontend screens can exist without `TheWorld` or `ThePlayer`. In-game HUD widgets may be recreated on reconnect, death, HUD rebuild, or screen transitions. Inspect the loader and neighboring official screen/widget before accessing runtime objects.

Use `AddClassPostConstruct` or the narrow supported hook for the target screen/widget. Preserve original layout, focus flow, callbacks, and controller navigation. Do not assume mouse input or a fixed resolution.

## Register input responsibly

Bind keys/buttons through the project's supported input layer and configuration. Respect text entry, chat, console, map, pause, active screens, focus, key repeat, controller ownership, and disabled controls. Avoid consuming a global input event when the feature is inactive.

Store handlers so they can be disabled or cleaned up. Detect conflicts with existing bindings and expose configurable keyboard/controller options when the action is user-facing. Input sends intent; the server validates gameplay.

## Handle audio

Register `SOUNDPACKAGE`/`SOUND` or the asset type used by the verified official pattern. Add `SoundEmitter` only to entities that play sounds. Use stable channel names for looping sounds so they can be updated or killed.

Stop loops on state exit, unequip, sleep, death, removal, screen close, and owner changes as applicable. Do not play renderer/client audio on dedicated servers. Avoid per-tick replay and uncontrolled voice overlap; rate-limit repeated combat/UI sounds.

Treat raw `.mp3`, `.wav`, project files, banks, and compiled `.fsb`/event packages according to the established build pipeline. Package only runtime-required outputs unless source audio is intentionally distributed.

## Support skins and visual identity

Use official skin/build/symbol helpers and inspect current skin-prefab patterns. Keep gameplay prefab identity separate from cosmetic build choice. Verify default build, skin build, inventory image, portrait, map icon, and symbol overrides, including missing or incompatible skins.

Clear overrides on unequip/state exit and avoid assuming every skin contains an optional symbol. Cosmetic choices must not change server authority or give hidden gameplay advantages.

## Add user/admin commands safely

Use supported user-command registration. Validate command arguments on the server and define permission, vote, local-only, and remote-execution behavior explicitly. Admin/debug commands must not trust a client-provided userid or target and must not expose private server data.

## Verify frontend features

Test main menu/no-world state, loading, host, remote client, reconnect, death/HUD rebuild, map and chat focus, multiple resolutions/HUD scales, keyboard/mouse, and controller. Test sound loops through interruption and entity sleep/removal.
