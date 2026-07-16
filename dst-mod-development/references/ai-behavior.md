# Brains, behaviour trees, and creature state

## Divide responsibilities

Use the brain/behaviour tree to choose goals and produce `BufferedAction`s. Use components for durable capabilities and authoritative state. Use the stategraph for animation-timed execution and interruption. Avoid placing damage, loot, or irreversible mutation only in a brain node or animation callback.

Attach brains and stategraphs on the master simulation unless an official client-prediction pattern explicitly requires otherwise. For extensions, prefer `AddBrainPostInit` and stategraph hooks over replacing global classes.

## Build behaviour trees safely

Start from the closest official brain and inspect every required component/tag. Order high-priority survival/combat nodes before idle work. Give chase, leash, wander, and action nodes explicit distance/time limits so an AI cannot pursue or retry forever.

Behaviour predicates and target functions run frequently. Keep them side-effect free, allocation-light, and based on bounded searches. Cache static tag tables outside callbacks. Validate targets again when the buffered action executes because entities can disappear or change state.

## Coordinate brain and stategraph

Respect `busy`, `moving`, `canrotate`, `attack`, `sleeping`, frozen, dead, and other relevant state tags. Do not push the same state repeatedly. Clear buffered actions and temporary combat targets when an action is interrupted or becomes invalid.

When adding a custom action, implement discovery, server execution, stategraph handlers, animation timeline, and failure/exit cleanup as one system. Test mount/platform/ghost cases only if the actor can encounter them.

## Handle sleep, wake, and removal

Brains and periodic targeting should not keep distant sleeping creatures active without need. Ensure sleep/wake does not duplicate tasks or lose required state. Stop sounds, emitters, and expensive searches on sleep; resume only what remains valid.

On removal, death, home destruction, leader loss, or migration, clear entity references and listeners. Home/leader/target references must be checked with `IsValid()` before use.

## Verify AI

Test idle, acquire target, lose target, blocked path, unreachable target, combat interruption, sleep/wake, save/load, leader/home removal, and mass-spawn performance. Verify the dedicated server log for behaviour-tree errors that may not appear on a client.
