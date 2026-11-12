# Observable Cores

When a JavaScript or TypeScript core owns a workflow, follow the
[XState guidance](javascript/xstate.md). When the core supplies observable
state to React, also read the
[React observable core defaults](javascript/react/observable-cores/observable-cores.md).

## Public Interface And State Ownership

Keep the core's own interface for commands, observation, and lifecycle. Preserve
an established public interface during a refactor unless its migration has
been agreed explicitly.

When introducing a machine into an established core, preserve its observation
contract. The machine owns workflow transitions and the public store projects
its snapshot. Keep unrelated core state with its own owner.

Model the decisions the core owns. Receiving a mode or target selected by another
owner does not transfer ownership of that selection to this core.

## Stable Identity And Observation

Keep the core instance, command surface, and public store stable for their
intended lifetime. State changes update observation snapshots rather than
replacing these handles.

Keep snapshot reads referentially stable while public state is unchanged. When
projecting machine snapshots, distinguish changes in public values from changes
in internal bookkeeping. Avoid duplicate public notifications for the same
transition and preserve unchanged selections for subscribers.

## Reactive Convergence

When external inputs remain unchanged and the requested work is complete,
reactive update chains must settle. Internal notifications, recalculations, or
writes must not continually reactivate one another. This applies to compositions
of stores, subscriptions, effects, and measurements, even when each participant
is correct in isolation.

Continuous activities such as acquisition, playback, and animation have an
explicit cause, intended lifetime, and stopping condition. Their intentional
updates do not exempt downstream reactions from convergence between inputs.

When introducing or changing a reactive update chain, review the complete
propagation path, including feedback into its sources. Verify through public
behavior at the composition boundary that:

- a finite input change settles after the required work;
- repeating semantically unchanged inputs does not restart propagation;
- stopping the activity or disposing its owner ends its work and releases the
  associated subscriptions, according to the lifecycle contract.

Exercise relevant differences in representation or precision between participants,
such as measured and rounded geometry. Keep tests bounded and deterministic so a
non-converging chain fails rather than hanging. Derive iteration or notification
bounds from the behavior being verified; universal render counts are not a
convergence contract.

## Resource Lifecycle

For dependencies, activation, ownership, and disposal contracts, read
[Stateful engines](stateful-engines.md).

## Verification

Test through the core's public commands, snapshots, subscriptions, and lifecycle.
Cover meaningful transitions and failures, ignored events, snapshot identity,
late results after reset or disposal, and reentrant callbacks where relevant.
Validate ordering and observation contracts rather than only final state values.
