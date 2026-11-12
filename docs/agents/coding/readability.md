# Readability

## File Layout Reveals Concepts

Treat file layout as part of developer experience, not as incidental storage.
Choose a module's location from the responsibility it owns. Place its policies,
state, and adapters under that responsibility, even when they consume data from
several other areas.

If a capability grows beyond a single local helper, give it a named home under
the nearest meaningful architectural role, such as adapters, policies,
resolvers, stores, providers, services, repositories, or controllers.

Separate reading external state, evaluating domain rules, persistence, and
transport mapping when they are distinct responsibilities. Choose module and directory names
from the feature's vocabulary rather than reproducing another feature's layout.

Choose folder depth to reveal ownership and dependencies, rather than to reach
a preferred number of files or levels.

When ownership changes, reassess the model, lifecycle, name, location, consumers,
and tests together; preserving behavior alone does not justify retaining a
layout that describes the previous ownership. For state and resource ownership,
read [Stateful engines](stateful-engines.md).

## Abstractions And Local Construction

Prefer visible expressions and typed objects when they already communicate the
operation and its contract. Base extraction on the responsibility it gives a
name and boundary.

An abstraction earns its indirection by owning a transformation, invariant,
domain operation, boundary adaptation, or shared mechanics that would otherwise
obscure its callers. Compare it with the direct alternative: identify the
responsibility or guarantee that removing it would lose, or the complexity that
would return to the callers.

Repeated data shape is not duplicated logic. Multiple callers, type preservation,
and a distinct public name are not sufficient reasons for an intermediate layer
when expressions or direct typed construction provide the same guarantees.

Keep simple construction local, using typed records, DTOs, or domain value
objects. Direct construction already exposes the model being created:

```java
record Notice(String code, String message) {}
Notice notice = new Notice("MISSING_ITEM", "Choose an item.");
```

A factory that only forwards the same constructor arguments adds no rule:

```java
Notice makeNotice(String code, String message) {
    return new Notice(code, message);
}
```

Renaming a forwarding method or moving it behind a facade does not by itself
remove an unnecessary boundary. Reassess the implementation's name and location,
and put the public operation with its implementation when no separate boundary
is needed. Retain an adapter or compatibility surface only for an actual constraint
that the direct alternative cannot satisfy.

Group operations that exclusively extend an existing API with that API. Keep
helpers with an independent responsibility or boundary independently owned.

## Names Label Concepts

Follow the feature's established naming family. Use ownership and descriptive
names to expose conceptual levels.

When the subject alone leaves an object's responsibility unclear, include its
role in the type name. Use short local names when the scope identifies the
subject, including role names for instances.

## Names Match Responsibilities

Name an abstraction after the responsibility it owns at its actual level of
abstraction.

A helper that owns a mechanical transformation should name the transformation:
case conversion, prefix handling, parsing, normalization, grouping, sorting, or
validation.

A boundary adapter that owns a full model conversion may name the source and
destination models.

A domain operation should name the domain action or invariant it applies.

Avoid names that only describe where the result will be used when the function's
body owns a more specific operation. Evaluate the complete call expression,
including its owning object or type. It should let the reader
predict the transformation and any input or output distinction needed at that
call site, without opening the implementation. Use that context when choosing
the operation's name.

Prefer operation names for narrow transformations:

```java
normalizeParameterName(value);
```

Avoid destination-only names when they hide the actual operation:

```java
toPersistedParameter(value);
```

## Context And Values

When an operation interprets values against external facts, distinguish its
evaluation context from the values being evaluated:

```java
TargetResult evaluateTarget(TargetContext context, Target target);
```

The context contains relevant facts, such as catalog or runtime snapshots.
Pass the subject being evaluated rather than its whole containing model.
Use this shape where it clarifies a real boundary. Keep simple signatures for
operations with simple inputs, and keep service dependencies with their owner.

## Construction And Readiness

For instance initialization, activation phases, and resource lifecycle, read
[Stateful engines](stateful-engines.md).

## Visible Decisions

Make meaningful evaluation stages visible through control flow and intermediate
names. Validation, live command execution, and persistence answer different
questions even when they contribute to one operation. A reader should see
which question each stage answers without reconstructing a long sequence of
result mutations. Preserve the intended execution order and transaction
boundaries; this does not require a helper for each conditional.

Use blank lines to separate semantic phases such as validation, calculation,
state transitions, external effects, and cleanup. Keep statements within one
operation together. Group by meaning rather than inserting a blank line after
every statement; formatting tools do not establish these semantic boundaries.

## Tests

Follow the project test layout for the responsibility under test. Verify
interactions between child responsibilities at their composition boundary. Keep fixtures local to their tests; give
genuinely shared fixtures an explicit home at their common scope. File movement
alone is not a reason to duplicate behavioral coverage.

Test behavior, state shape, invariants, and domain outcomes. Avoid tests whose
main value is asserting one-line forwarding helpers or property-access wrappers.

For retained state, verify the transitions that preserve, suspend, invalidate,
or reset it, including automatic transitions where relevant. Cover logical
identity surviving runtime recreation, partial invalidation, and recovery when
those behaviors belong to the contract. Choose cases at the owning model or
composition boundary.
