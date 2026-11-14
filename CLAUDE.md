# prodturbo

## Workflow

- Work starts from a GitHub issue: run `/work-issue <number>` in plan mode (see `.claude/commands/work-issue.md`). The command pins its own model and effort.
- Branch from an up-to-date `dev` as `issue-<N>-<short-slug>`, created with `gh issue develop <N> --base dev --name issue-<N>-<short-slug> --checkout`. This makes it a linked branch, so the issue and its PR show up in each other's **Development** section. Open PRs against `dev`.
- Reference the issue in every commit message, e.g. `feat: add X (#N)`.
- Never enable auto-merge. PRs stay open for manual review and merge.
- The default branch is `main`. A `Closes #N` in a PR into `dev` doesn't link the issue, and merging doesn't close it. Linking comes from the linked branch, and closing is manual: `gh issue close <N>` after merging.

## Checks

No tests, linter, or type checker are set up yet. When they exist, list the exact commands here so they run during the execute step:

- Tests: _TBD_
- Lint: _TBD_
- Type check: _TBD_
