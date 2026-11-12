# Observable Cores

## Workflow Modeling

Prefer XState for core workflows whose states, events, and decisions benefit from
visual inspection and editing. Visual comprehension is a design goal even for a
small interaction; asynchronous complexity is not the only reason to use a
machine. Keep calculations, transformations, and simple local affordances direct
when a workflow model would add little clarity.

Apply this choice to new workflows and substantial redesigns. Keep an incidental
change scoped to its purpose rather than automatically migrating its whole core.

## Public Interface And State Ownership

Keep the core's own interface for commands, observation, and lifecycle. Adapt
commands to machine events internally so consumers can use the core without
knowing XState. Preserve an established public interface during a refactor unless
its migration has been agreed explicitly.

When using TanStack Store for a new observable core, expose the public store as
`store`. Preserve existing observation contracts when introducing a machine into
an established core. The machine owns workflow transitions; the public store projects its
snapshot instead of independently deciding those transitions. Other core state
can remain outside the machine when it has a separate responsibility.

Model the decisions the core owns. Receiving a mode or target selected by another
owner does not transfer ownership of that selection to this core. Keep meaningful
states, events, guards, and operation lifecycles visible in the machine; named
actions and actors implement the work behind them.

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

In XState, associate state-scoped work with invoked actors so leaving the state
releases its observation or resources.

## Visual Authoring

When visual authoring is part of the workflow, keep machine definitions
discoverable and editable by the visual editor. Use
named implementations to separate the workflow structure from calculations and
I/O without hiding behavioral decisions inside an opaque action.

The repository source is authoritative. When introducing or restructuring a
machine, verify opening it in the editor, saving a visual transition change back
to source, and refreshing the diagram after a source change. Inspect the diff for
preserved types and implementation bodies as well as editor-added metadata or
transition options. Restore temporary verification changes before delivery.

If interactive editor verification is unavailable, report it as unverified
separately from passing automated checks.

## Verification And React Integration

Test through the core's public commands, snapshots, subscriptions, and lifecycle.
Cover meaningful transitions and failures, ignored events, snapshot identity,
late results after reset or disposal, and reentrant callbacks where relevant.
Validate ordering and observation contracts rather than only final state values.

When React owns a core, read [imperative lifecycles](react/imperative-lifecycles.md).
When adapting its state to rendering, read
[rendering subscriptions](react/optimization/rendering/subscriptions.md).
