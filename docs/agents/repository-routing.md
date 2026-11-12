# Repository Routing

Read `workspace.local.md` to resolve a target name to a directory. Use `workspace.example.md` as a starting point for a new machine. Resolve relative paths from the Agent Home root.

When the user supplies a directory path, use it directly. If a named directory has no local mapping and its path is unclear, ask for the path.

A work directory records its base directory and can specify an entry directory relative to that base. For a Git repository, the base is the primary checkout. A reference directory only needs its location and can also be the target of an explicit task. Follow any applicable project guidance you find in either kind of directory. Add or change its scope setup when the task includes that work.

When the task specifies a Git checkout, worktree, or branch, use Git to identify and verify the requested checkout before editing. For a branch, match Git's recorded branch name. For a worktree name, look for the corresponding local checkout directory.

Name linked worktree directories `<REPO_NAME>_<worktree-name>`. The worktree name identifies the directory. Read the branch from Git. For example, a directory named `PROJECT_dashboard` may contain the branch `codex/dashboard`.

Use `git worktree list --porcelain` from the primary checkout to inspect registered worktrees, including those elsewhere on the machine. Ask which checkout to use if the requested target is missing or ambiguous.

When an entry directory is configured, resolve it inside the selected checkout and read its `AGENTS.md` explicitly. Otherwise inspect the selected directory and the paths involved in the task for existing guidance. Read entry files for affected nested scopes in order from parent to child. For work spanning several directories, apply this sequence to each one.
