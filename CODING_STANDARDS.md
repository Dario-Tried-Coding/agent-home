# Coding Standards

For implementation, reorganization, and code review, read the [alignment protocol](docs/agents/alignment.md) and start with the [coding guide](docs/agents/coding/coding.md). Follow the language routes for the files in scope and read their required foundations before assessing the change. Read the work directory's standards and then any nested standards that apply to the files involved.

## Preserve Enforced Standards

Resolve check failures by bringing the implementation into conformance with the
applicable standard. Do not make a task pass by disabling checks, lowering their
severity, broadening exclusions, adding suppressions or baselines, bypassing
required workflows, or weakening the rule or its regression tests.

Apply an already documented exception only within its stated scope and record
the reason at the exception. If a check appears incorrect or the standard needs
to change, present the concrete case, the affected guarantee, and the proposed
correction to the user before weakening enforcement. Proceed when that change
is already authorized; ordinary implementation authorization does not authorize
relaxing a standard. In review, inspect changes to enforcement and exceptions
alongside the code they allow to pass.
