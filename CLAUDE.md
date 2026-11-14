# prodturbo

## Workflow

- Work starts from a GitHub issue: run `/work-issue <number>` in plan mode (see `.claude/commands/work-issue.md`). The command pins its own model and effort.
- One issue → one branch → one PR. During exploration `/work-issue` checks the issue's scope; if it spans several responsibilities or areas, the plan proposes sub-issues instead. Once the split is approved, they're created as GitHub sub-issues of the parent, which stays open as the tracking issue, and each one is worked with its own `/work-issue` run.
- Branch from an up-to-date `dev` as `issue-<N>-<short-slug>`, created with `gh issue develop <N> --base dev --name issue-<N>-<short-slug> --checkout`. This creates a linked branch of the issue. Open PRs against `dev`.
- Reference the issue in every commit message, e.g. `feat: add X (#N)`.
- Never enable auto-merge. PRs stay open for manual review and merge.
- The default branch is `main`. A `Closes #N` in a PR into `dev` doesn't link the issue, and merging doesn't close it. To link the issue and the PR in each other's **Development** section, `/work-issue` calls the GraphQL mutation `addCloseIssueReferences` after opening the PR. Merging into `dev` still doesn't close the issue, so close it manually with `gh issue close <N>` after merging.

## Checks

No tests, linter, or type checker are set up yet. When they exist, list the exact commands here so they run during the execute step:

- Tests: _TBD_
- Lint: _TBD_
- Type check: _TBD_
