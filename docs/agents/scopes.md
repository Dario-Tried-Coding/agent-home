# Agent Guidance Scopes

## Ownership

Keep each instruction in the narrowest scope that covers every place where it applies.

- **Agent Home scope.** Personal working agreements and reusable guidance. Route technology-specific guidance by its applicability. For example, a React convention that applies across projects belongs here even when its first consumer is a single app.
- **Repository scope.** Guidance that applies across one work repository, such as its workspace conventions or a React convention shared by its apps.
- **Nested scope.** Guidance for a specific app, package, module, or other subtree.

Keep one authoritative copy of each instruction. When another scope needs that guidance, route to it with a pointer that says when to read it.

## Scope Setup

Each configured scope has three files at its root.

- `AGENTS.md` is the canonical entry file. It defines the scope's boundary and points to guidance using explicit reading triggers.
- `CLAUDE.md` imports `@AGENTS.md` from the same directory.
- `GLOSSARY.md` defines vocabulary owned by the scope.

Include `CODING_STANDARDS.md` when the scope owns coding conventions. Add `docs/agents/` and `docs/adr/` when the scope has guidance or decisions to record.

When a document's scope has a parent in the same repository, open with a link to the corresponding parent document or to the parent's `AGENTS.md` if there is no counterpart. In a nested `CLAUDE.md`, put this parent link before the `@AGENTS.md` import.

Verify the setup by starting at each supported entry file. Confirm that every required reference exists and that its trigger identifies the tasks that require it. From a standalone checkout, verify that project-specific guidance is reachable within the checkout. From Agent Home, verify that routing reaches the entry directory in the intended checkout and then the applicable nested scopes.
