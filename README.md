# turboprod

AI-driven development that transforms concepts into production-ready solutions, from initial vision to successful delivery.

Turboprod is a GitHub issue-to-PR workflow for [Claude Code](https://claude.com/claude-code), packaged as the `issue` plugin in the `turboprod` marketplace.

- `/issue:start <N>` takes issue `#N` from plan to a pull request ready for review: explore, plan (and wait for your approval), branch, implement, review, and open a PR linked to the issue. Issues too broad for one PR are split into GitHub sub-issues instead.
- `/issue:setup-issue` sets up a repository for that workflow.

## Requirements

- Claude Code, run in plan mode for `/issue:start`.
- The [GitHub CLI](https://cli.github.com) (`gh`), signed in with `gh auth login`.
- A git repository with a GitHub remote and at least one commit on its default branch.

## Install in a project

In Claude Code, from the project:

```
/plugin install issue --marketplace cyyprian/turboprod
/issue:setup-issue
```

On Claude Code older than 2.1.275, install in two steps instead: `/plugin marketplace add cyyprian/turboprod`, then `/plugin install issue@turboprod`.

`/issue:setup-issue` creates a `dev` branch if there isn't one, adds the Workflow and Checks sections to `CLAUDE.md`, and adds the marketplace to `.claude/settings.json` so collaborators are prompted to install the plugin. It opens a PR into `dev` with these changes. Fill in the Checks commands it leaves as _TBD_ so `/issue:start` runs your tests and linters.

## Workflow

- Work starts from a GitHub issue: run `/issue:start <N>` in plan mode.
- Branches are cut from `dev` as `issue-<N>-<slug>`, and pull requests target `dev`.
- PRs are never auto-merged. Tell Claude to merge once you've reviewed one.
- See [CLAUDE.md](CLAUDE.md) for the full workflow, the release process and the project checks.

## Update

Installed copies update when a new version is released to `main`. To pull it:

```
/plugin marketplace update turboprod
```

## Migrating from `work-issue`

The plugin used to be `work-issue@prodturbo`. It doesn't update to the new name, so reinstall it:

```
/plugin uninstall work-issue@prodturbo
/plugin marketplace remove prodturbo
/plugin install issue --marketplace cyyprian/turboprod
```

In projects set up with the old `/setup-work-issue`, change `.claude/settings.json` to the marketplace `turboprod` (repo `cyyprian/turboprod`) and the plugin `issue@turboprod`, and update `/work-issue` to `/issue:start` in `CLAUDE.md`.

## License

[MIT](LICENSE)
