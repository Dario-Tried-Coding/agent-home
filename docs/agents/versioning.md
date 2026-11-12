# Commits

Use this reference for commit decisions that are not coding standards.

## Commit Shape

Keep each task's history shaped around logical units of work.

When refining a local, unpublished commit created by the current task, amend it
only after the identity check below succeeds. Create a new commit for a separate
logical change, a user-requested checkpoint, or work whose earlier commit has
already been shared.

## Commit Identity

After each successful commit or amend, capture its full SHA with
`git rev-parse --verify HEAD`. Keep it associated with this task and this
repository/worktree in the work context, including continuity summaries.

Before amending, establish that the recorded commit is still local and
unpublished. Inspect the worktree and index, and stage only changes belonging to
the task. Compare `HEAD` with the SHA recorded when the task made its previous
commit; a SHA freshly read from the current `HEAD` is not evidence of ownership.
Branch, author, and commit message are not substitutes for that recorded SHA.

Execute the comparison and amend in one conditional command sequence. For
example, set `task_commit_oid` to the previously recorded full SHA, then run:

```sh
test -n "${task_commit_oid:-}" &&
  test "$(git rev-parse --verify HEAD)" = "$task_commit_oid" &&
  git commit --amend --no-edit &&
  git rev-parse --verify HEAD
```

Adapt the message options when needed, retaining the conditional identity check.
After success, retain the emitted SHA as the task's new commit identity. This
check protects the agent's amend path; it does not lock out concurrent Git
operations or constrain the user's manual commands.

If the recorded SHA is missing, `HEAD` differs, or unpublished status is
uncertain, inspect the history and prefer a new commit for the authorized work.
Leave existing history intact rather than reconstructing ownership from a guess.

**Complete when:** the commit contains only the task's changes and its full SHA
is retained for subsequent refinements; an amend also passed the identity and
unpublished-status checks.
