# Shared Resources

When multiple consumers use the same contract, schema, catalog, or other input,
identify its authoritative source and owner. Make shared changes at that
source. When adopting a version, verify that consumers can obtain it through
the project's usual delivery mechanism. Check compatibility in the consumers
affected by the change.

For generated outputs, follow [generated artifacts](generated-artifacts.md).
When a consumer obtains the resource through a Git submodule, also follow
[Git submodules](../git-submodules.md).

## Document The Local Workflow

Put scope-specific guidance in `docs/agents/shared-resources.md` or link to a
maintained project guide that covers the workflow. Make it reachable through
the [scope's entry files](../scopes.md#shared-resources-and-generated-artifacts).

Identify the source owner and where the consumer declares the version it uses.
Show how to initialize or adopt the input, where to run the commands, how to
verify compatibility, and which dependency changes belong in the consumer
commit. Link to manifests and configuration for details they already declare.
