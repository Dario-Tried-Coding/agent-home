# Agent-Facing Markdown

For a new or changed scope, follow [scope ownership and setup](scopes.md). Keep one authoritative copy of each instruction and use a pointer from every entry file that needs to route to it. State the task or decision that triggers the read.

Keep `AGENTS.md` short. Put detailed guidance behind topic pointers. Use `CODING_STANDARDS.md` as the entry for coding guidance and [domain docs](domain.md) for the vocabulary and decisions that apply to a task.

## Organize Topic Guidance

Use a descriptive kebab-case filename for each reference. Keep a standalone
topic in one file. Organize nested guidance by owning area and responsibility.

Every topic directory under `docs/agents/` has a same-named Markdown router
from the first guide onward. For example, `viewer/viewer.md` routes to
`viewer/canvas.md`. A router links to guides or topic routers in its immediate
scope and gives each link a reading trigger. Entry files link to the
top-level topic router. Put a trigger for a specific technique in the nearest
router that owns it.

Distinguish required foundations from conditional topics. Route foundations by
the technology or file types in scope; reading them should not depend on the
agent first recognizing an architectural concern in the change. Use task-based
triggers for specialized topics. Include implementation and review wherever
both need the guidance.

Use ADRs for consequential decisions and follow the owning scope's existing
filename convention.

Point agents to the current source for values already visible in manifests, configuration, and code. Document conventions and reasons that cannot be discovered from those sources. Use examples that illustrate the rule independently of today's file layout.

## Codebase Patterns

When the user establishes a convention intended for reuse, capture it in the
coding standards of the narrowest scope that owns it. Compare nearby
implementations first to establish which parts of their file structure,
ownership, and lifecycle belong to the convention.

Keep a short standalone rule in the scoped `CODING_STANDARDS.md`. When the
pattern needs a guide, follow [topic guidance](#organize-topic-guidance) within
the same scope.

Describe the recurring requirements and cite a reference implementation. Keep
feature-specific decisions with their spec or ADR. Use the type system or a
focused automated check for requirements that can be verified mechanically.

## Explain Structure And Flow

Use `writing-for-agents` to organize agent-facing guidance and make its
requirements checkable. When the guide explains file layout, component
ownership, control flow, or lifecycle, use `show-me` to choose the smallest
useful sketch. Use a shallow file tree for layout, a call tree for lifecycle,
or pseudocode for conditional logic.

Keep rules and exceptions next to the sketch they qualify. Use code references
where the reader needs to inspect a reference implementation. In sketches,
prefer paths relative to a stated root or an alias defined by the project,
such as `@/` in HMI. Keep Markdown links for routing between guides.

## Verify The Guidance

Before finishing, follow the affected routes from their entry files to each
new or moved guide. Check each route against
[topic guidance](#organize-topic-guidance) and verify that each target resolves.
Update incoming links when a reference moves. Check the scope of each
instruction, term, decision, and reference against
[scope ownership](scopes.md#ownership).
