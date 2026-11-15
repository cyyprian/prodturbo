# turboprod

AI-driven development that transforms concepts into production-ready solutions, from initial vision to successful delivery.

Turboprod is a GitHub issue-to-PR workflow for [Claude Code](https://claude.com/claude-code), packaged as the `issue` plugin in the `turboprod` marketplace.

`/issue:start <N>` takes issue `#N` from plan to a pull request ready for review: explore, plan (and wait for your approval), branch, implement, review, and open a PR linked to the issue. Issues too broad for one PR are split into GitHub sub-issues instead.

The plugin needs no setup and writes nothing into your project's `CLAUDE.md`. On the first run, `/issue:start` offers to create the `dev` branch if the repository doesn't have one.

## Requirements

- Claude Code, run in plan mode for `/issue:start`.
- The [GitHub CLI](https://cli.github.com) (`gh`), signed in with `gh auth login`.
- A git repository with a GitHub remote and at least one commit on its default branch.

## Install in a project

In Claude Code, from the project:

```
/plugin install issue --marketplace cyyprian/turboprod
```

On Claude Code older than 2.1.275, install in two steps instead: `/plugin marketplace add cyyprian/turboprod`, then `/plugin install issue@turboprod`.

Each collaborator installs it the same way, once per machine.

`/issue:start` runs the test, lint and type-check commands your `CLAUDE.md` lists. If it lists none, it uses the ones the project defines (`package.json` scripts, a `Makefile`, `pyproject.toml` and so on), and asks if it finds none.

## Workflow

- Work starts from a GitHub issue: run `/issue:start <N>` in plan mode.
- Branches are cut from `dev` as `issue-<N>-<slug>`, and pull requests target `dev`.
- PRs are never auto-merged. Tell Claude to merge once you've reviewed one.
- The full workflow is in [`start.md`](plugins/issue/commands/start.md). This repository's own [CLAUDE.md](CLAUDE.md) covers its release process and checks.

## Update

Installed copies update when a new version is released to `main`. To pull it:

```
/plugin marketplace update turboprod
```

## Uninstall

```
/plugin uninstall issue@turboprod
```

Nothing is left to clean up in the project. The `dev` branch stays.

## Migrating from earlier versions

Before 3.0.0, `/issue:setup-issue` (or `/setup-work-issue`) copied the workflow into the project. That command is gone and `/issue:start` no longer needs the copy. In projects set up with it:

- Delete the `## Workflow` section from `CLAUDE.md`. Keep `## Checks` if it lists your commands.
- Remove the `turboprod` (or `prodturbo`) entry from `extraKnownMarketplaces` and `issue@turboprod` (or `work-issue@prodturbo`) from `enabledPlugins` in `.claude/settings.json`, unless you want to keep the plugin enabled for collaborators.

The plugin used to be `work-issue@prodturbo`. It doesn't update to the new name, so reinstall it:

```
/plugin uninstall work-issue@prodturbo
/plugin marketplace remove prodturbo
/plugin install issue --marketplace cyyprian/turboprod
```

## License

[MIT](LICENSE)
