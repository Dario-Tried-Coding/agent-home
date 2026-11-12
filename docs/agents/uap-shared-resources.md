# UAP Shared Resources

`UAP_WEB_SHARED` owns the contracts and catalogs consumed as a submodule by UAP projects. Read the consumer's own shared-resource guide for its integration details and verification commands.

Author shared changes in a separate checkout of `UAP_WEB_SHARED`. Publish the approved shared commit before recording a consumer reference to it. In each consumer, resolve the submodule path from `.gitmodules`, initialize it with `git submodule update --init --recursive`, and confirm its worktree is clean before adopting the approved commit. A normal update restores the commit recorded by the consumer. For a deliberate remote-tip adoption with `branch = .`, verify the matching branch exists and that the consumer is on that branch before updating.

Regenerate downstream outputs using the consumer's own instructions, check compatibility with affected consumers, and stage the submodule reference with the intended generated changes. To restore the recorded commit, preserve work in the submodule first and restore any staged reference before updating it. For a committed adoption, revert the consumer commit and update the submodule to the resulting reference.
