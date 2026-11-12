# React Imperative Lifecycles

Read this when React owns an imperative object or external subscription.

For the engine's operational boundary and resource ownership, read
[Stateful engines](../../stateful-engines.md). This reference defines how React
owns and mounts that engine.

## Own Imperative Objects In Effects

Evaluate the constructor, methods, and collaborators together to identify
subscriptions, timers, event listeners, network work, and external mutations.
For a React-owned imperative model with this work, use this lifecycle:

- create and initialize the object in `useEffect` or an effect event called by
  `useEffect`;
- store the mounted object in local state or context;
- render a nullable or not-ready state until the object exists;
- call `dispose()` on that specific instance from the corresponding effect
  cleanup and clear the mounted reference.

Use an effect event when initialization needs current values without making
those values reasons to recreate the instance. Keep dependencies that determine
instance identity explicit in the mounting effect.

## Stable Mounted Identity

Keep the mounted instance through ordinary rerenders. Choose effect dependencies
from the core's logical identity and lifecycle-defining dependencies; update
other inputs through its interface where supported. Workflow transitions belong
to the core's observable state, not repeated replacement of the mounted object.

When an established integration requires render-time construction, including a
`useMemo` or `useState` initializer, document that requirement and limit
construction to private in-memory initialization. Activate external work through
an idempotent `connect()` / `dispose()` pair called from an effect.

Verify that ordinary provider rerenders preserve the instance, while an identity
change releases the previous one and mounts its replacement. Observe cleanup and
subscription effects through the public interface rather than asserting absolute
render or construction counts across effect replay.

For hydration-backed controllers, exercise hydration available before mount,
effect replay, and unmount cleanup through React integration tests. Verify that
replay replaces effect-created instances and reconnects render-created instances
for integrations that require them. Verify that cleanup prevents late work from
updating a disposed instance in both cases, according to the operation's
cancellation contract.
