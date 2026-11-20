# React Imperative Lifecycles

Read this when React integrates an imperative object, external subscription,
or DOM resource, or when choosing effect timing.

For the engine's operational boundary and resource ownership, read
[Stateful engines](../../stateful-engines.md). This reference defines how React
owns and mounts that engine.

## Choose The Integration Boundary

Apply the ownership test in [React is an adapter](state-and-shape.md#react-is-an-adapter)
before choosing an integration mechanism. React supplies signals that depend on
its rendering and lifecycle; the core owns the policy and ordering those signals
trigger.

```text
React adapter
  user event -> core command
  DOM node connection/disconnection -> core connection/release operation
  committed input change -> core input update
Core
  owns resulting workflow, ordering, and resource policy
```

Use a callback ref when the integration follows the actual DOM node's connection
and disconnection. Use an effect when an external resource must synchronize with
committed React inputs or a resource's ownership follows the component's mounted
lifetime. Keep resource acquisition and release paired for the specific instance.
Choose against the actual integration contract rather than treating either
mechanism as a universal replacement for the other.

For each added or changed effect, identify the synchronization it performs and
why an existing event, node lifecycle signal, or core operation cannot directly
provide the required behavior. Use `useLayoutEffect` when correctness requires
its synchronous commit-phase timing before paint; identify the concrete failure
that ordinary effect timing would permit and compare other available signals.
An earlier cleanup alone does not establish that a layout effect is necessary.

Review both the adapter and the operations it calls. Complete the review when
each responsibility has an appropriate owner and the chosen mechanism is
justified by the required signal and timing. This comparison need not become a
checklist in the chat.

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
