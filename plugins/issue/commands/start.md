---
description: Take a GitHub issue from plan to a linked PR ready for review (explore → plan → execute → review → PR)
argument-hint: <issue-number>
model: opus
effort: high
---

You are working on GitHub issue #$ARGUMENTS. Follow these phases in order. Use the `gh` CLI for all GitHub operations.

## 0. Setup (read-only)
1. Tell me, in one line, the model, reasoning effort and permission mode you are running in. The effort for this run is `${CLAUDE_EFFORT}`: report that value, not a guess or a shell variable.
   - You are in plan mode only if the system has told you plan mode is active. If it hasn't, switch with the `EnterPlanMode` tool (load it with ToolSearch `select:EnterPlanMode` if it is deferred); I approve the switch. If the tool isn't available or I decline, say so in one line and continue; the approval gate in phase 2 still applies. Don't recommend restarting.
   - The `model` and `effort` set by this command last only until my next message. Approving the plan through ExitPlanMode keeps the run in one turn; anything I type afterwards (e.g. "go", changes to the plan, "merge") runs on the session's own model and effort. If the effort above isn't `high`, a `CLAUDE_CODE_EFFORT_LEVEL` environment variable is overriding it: tell me so in one line.
2. Run `gh issue view $ARGUMENTS --json title,body,comments,labels` and read the issue fully. (Use `--json`: `--comments` prints nothing when run without a terminal.)
3. Rename this session to `#$ARGUMENTS <issue title>` with `mcp__ccd_session_mgmt__set_session_title` (`session_id: "self"`). If the tool is deferred, load it first with ToolSearch (`select:mcp__ccd_session_mgmt__set_session_title`). If it isn't available (e.g. the CLI, not the desktop app), skip this step silently.
4. Make sure the working tree is clean (`git status`). If not, stop and tell me.
5. Find the default branch: `gh repo view --json defaultBranchRef -q .defaultBranchRef.name`. If this fails, stop and tell me (no GitHub remote, or `gh auth login` needed).
6. Check whether `dev` exists on the remote: `git ls-remote --heads origin dev`. If it doesn't, say so; it is created in phase 3.

Do not create branches, edit files, or post anything before the plan is approved.

## 1. Explore
- Find and read the code relevant to the issue. Do NOT edit any files in this phase.
- Note existing patterns, tests, and anything that constrains the solution.
- **Scope check.** Decide whether the issue is one unit of work (one branch, one reviewable PR). Signals that it should be split:
  - goals or acceptance criteria that are unrelated to each other;
  - work in separate areas or concerns (e.g. backend + UI + infra, or a feature + a refactor);
  - parts that could be shipped and reviewed independently;
  - an expected diff too large to review comfortably as one PR.

  Conclude with **Single unit** (and a one-line reason) or **Split recommended**. These are guidelines, not hard limits.

## 2. Plan
- Start the plan with a **Scope** section stating the scope-check verdict and why.
- **Single unit:** write a concise plan: goal, files to change, approach, test strategy, risks/open questions.
- **Split recommended:** instead of an implementation plan, propose the sub-issues. For each: a title, a 1–3 line body with acceptance criteria, and its dependencies/order. I may approve the split, adjust it, or override it and have you plan the issue as a single unit.
- **STOP and wait for my approval.** In plan mode, my approval is ExitPlanMode; otherwise wait until I say go. If I request changes, update the plan first.
- If an approved plan is a split, follow **Split path** below and skip phases 3–7.

## Split path (only when a split is approved)
1. Create each sub-issue and attach it to #$ARGUMENTS as a GitHub sub-issue:
   ```
   PARENT_ID=$(gh issue view $ARGUMENTS --json id -q .id)
   SUB_URL=$(gh issue create --title "<title>" --body "<body>

   Part of #$ARGUMENTS")
   SUB_ID=$(gh issue view "$SUB_URL" --json id -q .id)
   gh api graphql -f query='mutation($p:ID!,$s:ID!){addSubIssue(input:{issueId:$p,subIssueId:$s}){subIssue{number}}}' -f p="$PARENT_ID" -f s="$SUB_ID"
   ```
2. Comment on #$ARGUMENTS with a "## Split" heading listing the sub-issues in order, with their dependencies.
3. Stop. Do not create a branch or a PR. #$ARGUMENTS stays open as the tracking issue.

Finish by listing the sub-issue URLs and telling me to run `/issue:start <sub-issue-number>` for each, in dependency order.

## 3. Start the branch (after approval)
1. If `dev` doesn't exist on the remote, ask me, then create it from the up-to-date default branch: `git checkout <default-branch> && git pull && git checkout -b dev && git push -u origin dev`. Otherwise: `git checkout dev && git pull --prune`.
2. Create the branch as a linked branch of the issue:
   `gh issue develop $ARGUMENTS --base dev --name issue-$ARGUMENTS-<short-kebab-slug-from-title> --checkout`
3. Post the approved plan on the issue with a "## Plan" heading: `gh issue comment $ARGUMENTS --body-file <file>`.

## 4. Execute
- Implement the approved plan. Keep commits small and focused; reference the issue in each message, e.g. `feat: add X (#$ARGUMENTS)`.
- Run the project's tests, linter, and type checks. Use the commands the project's `CLAUDE.md` lists; if it lists none, use the ones the project defines (e.g. `package.json` scripts, a `Makefile`, `pyproject.toml`); if you find none, ask me. Fix failures before moving on.
- If the plan turns out to be wrong, stop and tell me instead of silently changing course.

## 5. Review
- Review your own diff: `git diff dev...HEAD`. Check it against the plan, look for leftover debug code, missing tests, and unrelated changes.
- Comment on the issue with a "## Work done" summary: what changed, any deviations from the plan, follow-ups.

## 6. Pull request
- `git push -u origin HEAD`
- Write the PR body (a summary, then a blank line and `Closes #$ARGUMENTS`) to a file, then `gh pr create --base dev --title "<concise title>" --body-file <file>`
- Link the PR and the issue in the Development section. `Closes #N` is ignored for PRs into a non-default branch, so link them explicitly through the API:
  ```
  ISSUE_ID=$(gh issue view $ARGUMENTS --json id -q .id)
  PR_ID=$(gh pr view --json id -q .id)
  gh api graphql -f query='mutation($i:ID!,$p:ID!){addCloseIssueReferences(input:{issueId:$i,pullRequestIds:[$p]}){clientMutationId}}' -f i="$ISSUE_ID" -f p="$PR_ID"
  ```
- Verify both directions: `gh pr view --json closingIssuesReferences` lists the issue, and `gh issue view $ARGUMENTS --json closedByPullRequestsReferences` lists the PR. If either is missing, tell me.

## 7. Hand off for review
- Do **not** enable auto-merge, and do not merge the PR until I explicitly tell you to. Leave it open for my review.

Finish by giving me the PR URL and a one-line status.

## 8. Merge (only when I tell you to merge)
1. `gh pr merge <PR> --squash --delete-branch`
2. `dev` is not the default branch, so merging doesn't close the issue. Close it: `gh issue close $ARGUMENTS`.
3. Switch the local checkout to an up-to-date `dev` and drop deleted remote branches: `git checkout dev && git pull --prune`.

Confirm the PR is merged, the issue is closed, and the local branch is `dev`.
