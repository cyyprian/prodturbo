# turboprod

## Workflow

- Work starts from a GitHub issue: run `/issue:start <number>` in plan mode. The command comes from the `issue` plugin in this repo (`plugins/issue/commands/start.md`, marketplace `turboprod`, enabled in `.claude/settings.json`) and pins its own model and effort.
- The plugin has no setup step and writes nothing into a consumer project's `CLAUDE.md` or settings. Keep `start.md` self-contained: every rule a consumer project needs lives there, not in this file.
- Installed copies of the plugin are read from `main` and update only when `version` in `plugins/issue/.claude-plugin/plugin.json` changes. Changes merged into `dev` reach projects only after a release (see **Release**). To try an edit before then, run `claude --plugin-dir plugins/issue`.
- One issue → one branch → one PR. During exploration `/issue:start` checks the issue's scope; if it spans several responsibilities or areas, the plan proposes sub-issues instead. Once the split is approved, they're created as GitHub sub-issues of the parent, which stays open as the tracking issue, and each one is worked with its own `/issue:start` run.
- Branch from an up-to-date `dev` as `issue-<N>-<short-slug>`, created with `gh issue develop <N> --base dev --name issue-<N>-<short-slug> --checkout`. This creates a linked branch of the issue. Open PRs against `dev`.
- Reference the issue in every commit message, e.g. `feat: add X (#N)`.
- Never enable auto-merge. PRs stay open for manual review. Merge only when the user explicitly says to; then squash-merge, close the issue, and switch the local checkout to an up-to-date `dev` (see phase 8 of `/issue:start`).
- The default branch is `main`. A `Closes #N` in a PR into `dev` doesn't link the issue, and merging doesn't close it. To link the issue and the PR in each other's **Development** section, `/issue:start` calls the GraphQL mutation `addCloseIssueReferences` after opening the PR. Merging into `dev` still doesn't close the issue, so it's closed explicitly with `gh issue close <N>` after merging.

## Release

Releases move `dev` to `main`, where installed copies of the plugin read it from. Release only when the user explicitly says to.

1. On a `dev` branch PR, bump `version` in `plugins/issue/.claude-plugin/plugin.json` (semver: patch for fixes, minor for new behavior, major for breaking changes to the workflow). Without a bump, installed copies don't update.
2. Run `claude plugin validate .` and `claude plugin validate plugins/issue`.
3. Open a PR from `dev` into `main` titled `Release vX.Y.Z`, listing the changes since the last release.
4. Merge it with a **merge commit** (`gh pr merge <PR> --merge`), not a squash. Don't delete `dev`.
5. The merge commit exists only on `main`, so fast-forward `dev` to it to keep `main` an ancestor of `dev` (the trees are identical, so this changes no files):
   ```
   git fetch && git checkout dev && git merge --ff-only origin/main && git push origin dev
   ```
6. Tag and publish. Push the tag first: `gh release create` asks for the `workflow` token scope when it has to create the tag itself.
   ```
   git tag -a vX.Y.Z origin/main -m vX.Y.Z && git push origin vX.Y.Z
   gh release create vX.Y.Z --verify-tag --title vX.Y.Z --generate-notes
   ```
7. Users update with `/plugin marketplace update turboprod`.

## Checks

No tests, linter, or type checker are set up yet. When they exist, list the exact commands here so they run during the execute step:

- Validate the marketplace and plugin: `claude plugin validate . && claude plugin validate plugins/issue`
- Tests: _TBD_
- Lint: _TBD_
- Type check: _TBD_
