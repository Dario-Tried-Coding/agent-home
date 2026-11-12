# Project Alignment

Use this protocol when implementing, reorganizing, or reviewing code. Apply it
within the authorized scope of the task. Its completion criteria are part of
finishing the work, alongside functional correctness.

## Principles

Treat organization and code shape as part of the design. A reader should be
able to discover responsibilities, ownership, dependencies, and domain
relationships through paths, names, contracts, and control flow.

Match the project's way of expressing those concepts. User examples illustrate
the underlying preference; use that preference to examine the rest of the
change rather than treating the examples as an exhaustive correction list.

Choose the simplest structure that exposes the relevant concepts. Folder
depth, name length, context objects, and helper counts are consequences of that
choice, not targets to maximize.

Use the applicable coding standards for detailed rules. Use the [coding guide](coding/coding.md) for shared criteria and the
applicable scopes for project-specific standards.

## 1. Establish The Relevant Conventions

Read the applicable instructions, standards, and domain decisions. Inspect a
small set of representative implementations with comparable responsibilities.
For each new or changed public contract, trace a representative real consumer
through the exported entrypoint it actually uses, including intermediate
wrappers, to the observable effect. Inspect tests at that boundary. For edits
without public-contract changes, the surrounding module may provide enough
evidence.

Inspect existing tools, libraries, and extension points that could serve the
responsibility before proposing a new mechanism.

Examine file organization, naming, type ownership, function signatures,
dependency direction, control flow, and test placement. Expand the search only
where an important design choice remains unresolved.

Prefer explicit user direction and documented standards over inferred
conventions. Evaluate examples for relevance: generated output, legacy code,
and code from earlier agent runs are not authoritative merely because they
exist. If examples conflict, identify which convention applies to this scope
and why. Surface conflicts affecting agreed decisions before changing them.

**Complete when:** each structural or public-contract choice the task needs has
an applicable standard or a relevant example, or is identified as a new choice
with a concrete reason. Keep this evidence and the consumer traces available
for the final comparison.

## 2. Shape The Change Before Expanding It

Identify the responsibilities being introduced or changed, their owners, the
dependencies they need, and how consumers reach them. Choose paths, exported
names, contracts, and test locations against the conventions established above.

For each proposed abstraction or mechanism, compare a concrete direct
implementation and using or extending an existing capability with introducing
another one. Apply the [readability criteria](coding/code-quality/readability.md#abstractions-and-local-construction)
to identify what responsibility, guarantee, or reduction in complexity the
addition provides, including its effects on configuration and maintenance.
Scripts, test runners, build wrappers, and configuration loaders are examples,
not an exhaustive list.

Resolve routine choices autonomously from the available evidence. Seek user
input when conflicting requirements or a consequential new choice cannot be
resolved within existing authorization. An established convention does not
need repeated approval.

**Complete when:** each new module and public contract has a clear owner and a
justified shape, and each added mechanism has been compared with the relevant
existing capabilities. Any departure from an applicable convention has a
task-specific reason. Scale this preparation to the size of the change; it need
not become a separate document or user approval step.

## 3. Implement With The Comparison In View

Carry the chosen conventions through the implementation, including types,
internal names, signatures, result construction, and tests. Revisit the design
when implementation reveals a different responsibility or dependency.

After a structural correction from the user, identify the underlying principle
and reexamine the affected responsibility, including alternatives introduced by
the correction itself. Apply the principle to analogous cases within the task's
scope rather than stopping at the named example or expanding into an unrelated
repository-wide cleanup.

Keep behavior and structural work within the task's authorized boundaries. In
a behavior-preserving refactor, verify the observable contracts as well as the
main path: identity, result shape, diagnostics, ordering, subscriptions, transaction
boundaries, and external side effects where relevant. When changed behavior needs regression coverage, exercise it through
the public entrypoint identified in step 1. Run the checks appropriate to the
change.

**Complete when:** the implementation and its consumers follow the chosen
structure, required checks pass, and any discovered departure has been resolved
or explicitly accounted for. A correction is complete when its principle holds
across the affected work, including the replacement solution.

## 4. Compare The Final Change

Inspect the final diff, including added paths and exported definitions. Compare
it with the applicable standards and representative examples, even if the
functional checks are green. Account for every new module and every added or
changed public contract; assess internal code where its shape affects clarity.

In review, treat the author's rationale as a hypothesis to verify against the
code and standards. Independently verify each trace established in
step 1, including the actual exported entrypoint and its composition. Check the
comparison for each abstraction or mechanism added or retained by the refactor,
including accompanying configuration changes. Identify concrete alternatives that achieve the required
behavior with less complexity and resolve avoidable additions. Passing tests
establish behavioral evidence, not structural conformance; review both. Fewer
files alone do not establish a better design.

Distinguish a documented requirement, an evidenced local convention, and a
reviewer's personal preference. Correct violations and unjustified
inconsistencies. Preserve a justified departure when it better serves the task;
state the reason rather than silently introducing a new convention. If a simpler
alternative would change an agreed decision, bring the trade-off to the user
before changing direction.

**Complete when:** every new or changed public contract has been verified from
a real consumer through its exported entrypoint to the observable effect, no
known standards violation or unexplained inconsistency or avoidable mechanism
remains in scope, and alternatives affecting agreed decisions have been raised. The final report identifies
material departures and unresolved limitations; routine conformance does not
require a checklist in the chat.
