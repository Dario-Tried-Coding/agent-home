# Git Submodules

Read the project's integration guidance and `.gitmodules` to identify the
submodule path and its role. Run consumer commands from the parent checkout.

## Restore The Recorded Commit

Use `git submodule update --init --recursive` to initialize the submodules and
check out the commits recorded by the parent. Before updating a populated
submodule, check its worktree for local edits and preserve any work in progress.

## Adopt A Different Commit

Select the intended commit in the submodule and confirm that other checkouts
can fetch it from the configured remote. Use a remote-tracking tip only when
the task calls for it and the configured tracking branch is the intended one.
Run the consumer's generation and verification steps when its integration
requires them.

Stage the submodule path in the parent to record the selected commit. Review
`git diff --cached --submodule=log` and include any intended downstream changes
in the parent commit.
