# prodturbo

## Workflow

- Work starts from a GitHub issue: run `/work-issue <N>` in Claude Code, in plan mode.
- Issues too broad for one PR are split into sub-issues during planning, and each sub-issue is worked separately.
- Branches are cut from `dev` as `issue-<N>-<slug>`, and pull requests target `dev`.
- See [CLAUDE.md](CLAUDE.md) for the full workflow and project checks.

## Use in another project

`/work-issue` ships as the `work-issue` plugin in this repo's `prodturbo` marketplace, so every project runs the same, up-to-date workflow. In Claude Code, from the other project:

```
/plugin install work-issue --marketplace cyyprian/prodturbo
/setup-work-issue
```

On Claude Code older than 2.1.275, install in two steps instead: `/plugin marketplace add cyyprian/prodturbo`, then `/plugin install work-issue@prodturbo`.

`/setup-work-issue` creates a `dev` branch if there isn't one, adds the Workflow and Checks sections to `CLAUDE.md`, and adds the marketplace to `.claude/settings.json` so collaborators are prompted to install the plugin. It opens a PR into `dev` with these changes. This repo is private, so installing it needs access to the repo.
