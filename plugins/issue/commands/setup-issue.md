---
description: Set up the current repo for the /turboprod:issue workflow (dev branch, CLAUDE.md workflow, plugin settings)
disable-model-invocation: true
---

Set up the current repository for the `/turboprod:issue` workflow. Use the `gh` CLI for all GitHub operations. Ask me before every step that changes something outside the working tree (creating or pushing branches, opening a PR), and never merge anything.

## 1. Check (read-only)
1. Confirm this folder is a git repository with a GitHub remote: `gh repo view --json nameWithOwner,defaultBranchRef`. If it isn't, stop and tell me.
2. Confirm `gh auth status` succeeds. If it doesn't, stop and tell me to run `gh auth login`.
3. Make sure the working tree is clean (`git status`). If not, stop and tell me.
4. If the repository has no commits yet, stop and tell me to push an initial commit to the default branch first: the `dev` branch is cut from it.
5. Report what is already in place: a `dev` branch (local and on `origin`), a `## Workflow` section in `CLAUDE.md`, and the `cyyprian` marketplace and `turboprod@cyyprian` entries in `.claude/settings.json`. Skip any step below whose result is already there.

## 2. `dev` branch
If `origin/dev` doesn't exist, ask me, then create it from the up-to-date default branch and push it:
```
git checkout <default-branch> && git pull
git checkout -b dev && git push -u origin dev
```
If `dev` exists, `git checkout dev && git pull`.

## 3. Setup branch
Create `chore/setup-turboprod` from `dev`. Make every file change below on it.

## 4. `CLAUDE.md`
Add the section below, replacing `<default-branch>` with the repository's default branch. Create `CLAUDE.md` with a `# <repo name>` heading if it doesn't exist. If it already has a `## Workflow` or `## Checks` section, don't overwrite it: show me how it differs from the section below and ask what to keep.

```markdown
## Workflow

- Work starts from a GitHub issue: run `/turboprod:issue <number>` in plan mode. The command comes from the `turboprod` plugin (marketplace `cyyprian`) and pins its own model and effort.
- One issue → one branch → one PR. During exploration `/turboprod:issue` checks the issue's scope; if it spans several responsibilities or areas, the plan proposes sub-issues instead. Once the split is approved, they're created as GitHub sub-issues of the parent, which stays open as the tracking issue, and each one is worked with its own `/turboprod:issue` run.
- Branch from an up-to-date `dev` as `issue-<N>-<short-slug>`, created with `gh issue develop <N> --base dev --name issue-<N>-<short-slug> --checkout`. This creates a linked branch of the issue. Open PRs against `dev`.
- Reference the issue in every commit message, e.g. `feat: add X (#N)`.
- Never enable auto-merge. PRs stay open for manual review. Merge only when explicitly told to; then squash-merge, close the issue, and switch the local checkout to an up-to-date `dev`.
- The default branch is `<default-branch>`. A `Closes #N` in a PR into `dev` doesn't link the issue, and merging doesn't close it. `/turboprod:issue` links the issue and the PR through the GraphQL mutation `addCloseIssueReferences`, and closes the issue explicitly with `gh issue close <N>` after merging.

## Checks

List the exact commands here so they run during the execute step of `/turboprod:issue`:

- Tests: _TBD_
- Lint: _TBD_
- Type check: _TBD_
```

Fill in the Checks commands if the repository makes them obvious (for example `package.json` scripts, a `Makefile`, `pyproject.toml`), and tell me which ones you filled in.

## 5. `.claude/settings.json`
Add these entries so everyone who opens the repository in Claude Code is prompted to install the plugin. If the file exists, merge them into it and keep every other setting.

```json
{
  "extraKnownMarketplaces": {
    "cyyprian": {
      "source": { "source": "github", "repo": "cyyprian/turboprod" }
    }
  },
  "enabledPlugins": {
    "turboprod@cyyprian": true
  }
}
```

## 6. Commit and PR
1. Show me `git diff` and ask before committing.
2. Commit with `chore: set up turboprod workflow`.
3. Ask me before pushing. Then `git push -u origin HEAD` and `gh pr create --base dev --title "Set up turboprod workflow" --body "<summary of the changes>"`.
4. Don't enable auto-merge and don't merge the PR.

Finish by giving me the PR URL (or the reason you stopped) and reminding me to fill in any Checks still marked _TBD_.
