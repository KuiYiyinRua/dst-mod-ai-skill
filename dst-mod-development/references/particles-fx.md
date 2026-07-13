# Particle FX and VFX

## Choose the effect model

Use an animated prefab for a small, deterministic visual effect. Use `VFXEffect`/emitters for many particles or envelope-driven visuals. Keep cosmetic effects independent from gameplay authority: an FX must not be the sole carrier of hit, cost, cooldown, or damage logic.

Decide explicitly whether the server spawns one replicated FX or each client creates a local visual. A replicated visual can be observed by late/remote clients; local cosmetic effects reduce network cost but may be missing or duplicated. Do not rely on a client-only effect existing on a dedicated server.

## Construct a particle prefab

Declare texture and shader assets, resolving paths using the established project pattern. Add `Transform` and `Network`, add the `FX` tag when appropriate, call `SetPristine`, and set `persists = false` for transient effects. Return before renderer-only setup on dedicated servers.

Initialize global envelopes once, with stable namespaced envelope identifiers. Configure render resources, max particle count, lifetime, blend mode, sort order/offset, bloom, and follow/kill behavior deliberately. Bound emission rate and maximum particles; randomization must not allow unbounded growth.

## Clean up visual state

Remove emitters/listeners/tasks when the entity is removed or animation ends. If attaching to a parent, handle parent removal and migration. Avoid a permanent emitter for a one-shot effect. Restore any symbols, colors, scale, light, bloom, and visibility changed on an actor.

## Verify FX

Test host, remote client, dedicated server, long play sessions, mass spawning, target removal mid-effect, and camera distance. Confirm missing texture/shader paths fail visibly during development rather than silently using an incorrect fallback.
