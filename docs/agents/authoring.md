# Agent-Facing Markdown

For a new or changed scope, follow [scope ownership and setup](scopes.md). Keep one authoritative copy of each instruction and use a pointer from every entry file that needs to route to it. State the task or decision that triggers the read.

Keep `AGENTS.md` short. Put detailed guidance behind topic pointers. Use `CODING_STANDARDS.md` as the entry for coding guidance and [domain docs](domain.md) for the vocabulary and decisions that apply to a task.

Use a descriptive kebab-case filename for each reference. Start a topic with one file. When it needs several guides with different reading triggers, give it a directory and a same-named router that explains when to read each guide. Use ADRs for consequential decisions and follow the owning scope's existing filename convention.

Point agents to the current source for values already visible in manifests, configuration, and code. Document conventions and reasons that cannot be discovered from those sources. Use examples that illustrate the rule independently of today's file layout.

Before finishing, follow the affected routes from their entry files and verify every new or moved reference. Update incoming links when a reference moves. Check the scope of each instruction, term, decision, and reference against [scope ownership](scopes.md#ownership). Verify that each topic's trigger identifies when to read it.
