# Networking and authority

## Decide the data flow

| Need | Mechanism | Owner |
|---|---|---|
| Client requests an action | `SendModRPCToServer` + `AddModRPCHandler` | Server validates and mutates |
| Server exposes durable/current state | `net_*` variable, replica, dirty event | Server writes; clients read |
| Server sends a targeted transient notification | Supported client RPC or targeted classified channel | Server sends; client presents |
| Code communicates inside one simulation | `PushEvent`, callback, component method | Local only |

Do not treat one mechanism as universally mandatory. Choose by direction, lifetime, audience, ordering, and reconnection needs. Prefer replicated state when late joiners or reconnecting clients must observe the current value. Prefer a transient message only when missing old messages is acceptable.

## Build networked prefabs

In common initialization, add `Transform`, `AnimState`, `Network`, tags needed by classification, and all net variables. Call `inst.entity:SetPristine()`, then return on non-master simulations before adding server components.

Use unique, namespaced netvar debug names and dirty-event names. Choose the smallest type that covers the real domain, but do not trade correctness for a few bytes. Update only when values change. Remember that dirty events signal changes; consumers should also initialize their view from `:value()`.

## Secure RPC handlers

For every client-to-server RPC:

1. Confirm `player` is valid and alive when required.
2. Validate types, numeric finiteness, ranges, enums, and string lengths.
3. Resolve target entities on the server and verify distance, visibility, ownership, inventory, recipe, cooldown, and permissions.
4. Rate-limit spam-prone requests.
5. Ignore or reject invalid requests without partially mutating state.

Never accept client-computed damage, cost, rewards, inventory removal, or success results as authoritative.

Namespace RPC names and keep argument order/version stable. For request/response flows, include a bounded correlation identifier only when simultaneous requests are possible; reject stale or duplicate requests. Do not serialize arbitrary Lua tables or executable data through strings.

Use shard RPCs only for communication between world processes. Validate payloads again on the receiving shard and design for delay, duplicate delivery, missing shards, and player migration during the request.

## Replicas and UI

Use a component for server behavior and a replica for client-readable state or action tests. Keep their public method semantics aligned. Guard component access by execution side; guard replica access by existence.

Widgets should subscribe to replicated state, render it, and clean up callbacks in their teardown path. Avoid polling when a dirty event exists. A widget may use globals made available by its actual environment, but never rely on UI code for authoritative mutation or persistence.

Place private owner-only data on the appropriate classified entity rather than a world-visible entity. Treat netvar ranges and string sizes as protocol constraints: clamp before `:set`, avoid high-frequency large strings, and split state semantically rather than building an unbounded serialized blob.

## Prediction

Use prediction only for responsiveness. The server remains authoritative and must be able to correct the client. Avoid prediction for irreversible effects. When extending actions or stategraphs, check both server and client stategraphs, action handlers, locomotor ownership, and buffered-action cleanup.

## Multiplayer review

Check host, remote client, dedicated server, late join, reconnect, entity removal during RPC, and high latency. For player-specific state, verify owner-only versus globally visible behavior and avoid leaking private data through global netvars.
