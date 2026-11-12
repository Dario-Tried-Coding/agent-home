# XState

## Workflow Modeling

Read the [XState decision](../../../adr/0002-use-xstate-for-imperative-workflows.md)
when choosing a workflow model or revisiting the trade-off.

Use XState by default when introducing or substantially redesigning a
stateful workflow, including workflows owned by imperative cores. Visual
comprehension can justify a machine even for a small interaction. Keep simple
calculations and transformations direct. Scope migrations of existing
workflows explicitly.

Keep meaningful states, events, guards, and operation lifecycles visible in
the machine. Use named actions and actors for the work they trigger.

## Core Integration

Read [observable core contracts](../observable-cores.md) for the public command,
snapshot, and lifecycle contracts. Adapt commands to machine events
internally so consumers can use the core through its own interface.

Associate state-scoped work with invoked actors so leaving the state
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
