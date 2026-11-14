# prodturbo

## Workflow

- Work starts from a GitHub issue: run `/work-issue <number>` (see `.claude/commands/work-issue.md`).
- Branch from an up-to-date `dev` as `issue-<N>-<short-slug>`; open PRs against `dev`.
- Reference the issue in every commit message, e.g. `feat: add X (#N)`.
- The default branch is `main`, so `Closes #N` does not auto-close issues on merge into `dev` — close them with `gh issue close <N>` after merging.

## Checks

No tests, linter, or type checker are set up yet. When they exist, list the exact commands here so they run during the execute step:

- Tests: _TBD_
- Lint: _TBD_
- Type check: _TBD_
