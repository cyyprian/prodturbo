# prodturbo

A GitHub issue-to-PR workflow for [Claude Code](https://claude.com/claude-code), packaged as the `work-issue` plugin.

- `/work-issue <N>` takes issue `#N` from plan to a pull request ready for review: explore, plan (and wait for your approval), branch, implement, review, and open a PR linked to the issue. Issues too broad for one PR are split into GitHub sub-issues instead.
- `/setup-work-issue` sets up a repository for that workflow.

## Requirements

- Claude Code, run in plan mode for `/work-issue`.
- The [GitHub CLI](https://cli.github.com) (`gh`), signed in with `gh auth login`.
- A git repository with a GitHub remote and at least one commit on its default branch.

## Install in a project

In Claude Code, from the project:

```
/plugin install work-issue --marketplace cyyprian/prodturbo
/setup-work-issue
```

On Claude Code older than 2.1.275, install in two steps instead: `/plugin marketplace add cyyprian/prodturbo`, then `/plugin install work-issue@prodturbo`.

`/setup-work-issue` creates a `dev` branch if there isn't one, adds the Workflow and Checks sections to `CLAUDE.md`, and adds the marketplace to `.claude/settings.json` so collaborators are prompted to install the plugin. It opens a PR into `dev` with these changes. Fill in the Checks commands it leaves as _TBD_ so `/work-issue` runs your tests and linters.

## Workflow

- Work starts from a GitHub issue: run `/work-issue <N>` in plan mode.
- Branches are cut from `dev` as `issue-<N>-<slug>`, and pull requests target `dev`.
- PRs are never auto-merged. Tell Claude to merge once you've reviewed one.
- See [CLAUDE.md](CLAUDE.md) for the full workflow, the release process and the project checks.

## Update

Installed copies update when a new version is released to `main`. To pull it:

```
/plugin marketplace update prodturbo
```

## License

[MIT](LICENSE)
