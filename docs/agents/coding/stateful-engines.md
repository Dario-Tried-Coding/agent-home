# Stateful Engines

## Explicit Owners

Prefer an instance-owned class for a capability that coordinates mutable state,
operations, or resources over time. Keep its dependencies, commands, and lifecycle
visible through that instance, including when no observable state is needed.
Module-level collections or promises with exported functions can hide these
responsibilities; a separate file alone does not establish an owner.

Keep pure transformations, validators, and simple configuration direct. Choose
an engine because it owns behavior and invariants, rather than because an
implementation happens to contain a collection. Observation and workflow
representation follow the capability's needs; an engine does not by itself
require a public store or a state machine.

State belongs to the logical owner whose lifetime it follows. Distinguish that
identity from temporary instances that display or operate on it. A queue shared
by several consumers must retain its ordering guarantee when one consumer is
replaced; make the shared instance and its lifetime explicit. Replace an
instance when its logical identity or lifecycle-defining dependencies change,
releasing the previous instance's owned resources.

## Complete Operational Boundary

Provide the dependencies needed for the engine to manage its own operation.
Keep internal subscriptions, batching, retries, and collaborator activation with
the engine that owns their policy. The consumer constructs or acquires the
instance, invokes its public commands, and releases what it owns.

An injected callback may adapt an external effect. Keep decisions about when
and in what order to invoke it inside the engine. Examine the callback body as
well as the class: delegating an internal workflow back to each consumer leaves
the operational boundary incomplete.

## Construction And Resources

Construct instances with the inputs required to establish their initial state
and invariants. Reserve update operations for subsequent changes.

Prefer constructing exclusively owned collaborators inside the engine when
callers have no meaningful choice of implementation, configuration, sharing, or
lifetime. Receive dependencies from outside when they are externally owned,
shared, or required by a concrete integration constraint. Ease of mocking alone
does not justify exposing an internal collaborator as a required constructor
parameter.

Separate activation from construction when activation represents a meaningful
lifecycle phase or satisfies an integration constraint. Make that phase and its
ownership explicit at call sites.

The owner releases its internal subscriptions, timers, and owned collaborators
through its lifecycle interface, while respecting borrowed dependencies. Define
reset and disposal against the actual operation: stopping observation,
cancelling a transport request, and reversing a remote effect are different
contracts. Ensure an abandoned operation cannot update a disposed engine or a
newer request. Preserve callback ordering promised by the public interface,
including callbacks that synchronously issue another command.

## Example: Coalescing Changes

A change buffer owns the rule that repeated keys are coalesced until drained.
Its caller chooses the buffer's lifetime; the collection is private instance
state.

```ts
class Change_Buffer {
  private readonly pending = new Set<string>();

  record(key: string) {
    this.pending.add(key);
  }

  drain(): string[] {
    const keys = [...this.pending];
    this.pending.clear();

    return keys;
  }
}
```

## Review The Engine And Its Consumers

For each changed engine, identify its state and resource owners, intended
lifetime, required dependencies, and public operations. Follow its consumers
through construction, use, replacement, and release, including injected
callbacks. Check whether they still coordinate an internal workflow or whether
consumer replacement discards a guarantee owned by a longer-lived resource.

For each injected collaborator, inspect real callers and tests to identify the
choice or integration requirement the parameter serves. Check whether callers
make that choice or merely assemble an internal detail, and whether construction
and release follow the same owner.

Complete the review when each operational step has the appropriate owner,
activation phases have a concrete reason, and resource release preserves the
operation's ordering and cancellation contract. Verify these contracts through
the public interface when behavior changes.
