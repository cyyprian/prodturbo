# prodturbo

## Workflow

- Work starts from a GitHub issue: run `/work-issue <N>` in Claude Code, in plan mode.
- Issues too broad for one PR are split into sub-issues during planning, and each sub-issue is worked separately.
- Branches are cut from `dev` as `issue-<N>-<slug>`, and pull requests target `dev`.
- See [CLAUDE.md](CLAUDE.md) for the full workflow and project checks.
