# XState

## Workflow Modeling

Prefer XState for core workflows whose states, events, and decisions benefit from
visual inspection and editing. Visual comprehension is a design goal even for a
small interaction; asynchronous complexity is not the only reason to use a
machine. Keep calculations, transformations, and simple local affordances direct
when a workflow model would add little clarity.

Apply this choice to new workflows and substantial redesigns. Keep an incidental
change scoped to its purpose rather than automatically migrating its whole core.

## Core Integration

Read [observable cores](../observable-cores.md) for the public command,
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
