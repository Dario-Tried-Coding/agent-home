# Generated Artifacts

If the code or build you are working on uses generated artifacts, identify the
authoritative source of each affected artifact. Change the source, schema,
manifest, or generator that owns the desired behavior.
Find the project's workflow through the owning scope's
[entry files](../scopes.md#shared-resources-and-generated-artifacts). Use its
generation and verification steps to update downstream output, then review
the resulting changes. When the project designates an artifact as
a maintained source, follow that ownership.

## Document The Local Workflow

Put scope-specific generation guidance in `docs/agents/generated-artifacts.md`
or link to a maintained project guide that covers the workflow. If generation
is part of adopting a shared input, include it in the local shared-resource
guide.

Identify the inputs, the generator, and the outputs. Link to the scripts or
configuration that own those details. Show which commands to run, where to
run them, how to verify the result, and which generated changes belong in the
consumer commit.
