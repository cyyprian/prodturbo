---
description: Take a GitHub issue from plan to a linked PR ready for review (explore → plan → execute → review → PR)
argument-hint: <issue-number>
model: opus
effort: high
---

You are working on GitHub issue #$ARGUMENTS. Follow these phases in order. Use the `gh` CLI for all GitHub operations.

## 0. Setup (read-only)
1. Tell me, in one line, the model, reasoning effort and permission mode you are running in. If this session is not in plan mode, recommend that I restart `/work-issue` in plan mode, then continue.
2. Run `gh issue view $ARGUMENTS --comments` and read the issue fully.
3. Make sure the working tree is clean (`git status`). If not, stop and tell me.

Do not create branches, edit files, or post anything before the plan is approved.

## 1. Explore
- Find and read the code relevant to the issue. Do NOT edit any files in this phase.
- Note existing patterns, tests, and anything that constrains the solution.

## 2. Plan
- Write a concise plan: goal, files to change, approach, test strategy, risks/open questions.
- **STOP and wait for my approval.** In plan mode, my approval is ExitPlanMode; otherwise wait until I say go. If I request changes, update the plan first.

## 3. Start the branch (after approval)
1. `git checkout dev && git pull`
2. Create the branch as a linked branch of the issue:
   `gh issue develop $ARGUMENTS --base dev --name issue-$ARGUMENTS-<short-kebab-slug-from-title> --checkout`
3. Post the approved plan on the issue with a "## Plan" heading: `gh issue comment $ARGUMENTS --body-file <file>`.

## 4. Execute
- Implement the approved plan. Keep commits small and focused; reference the issue in each message, e.g. `feat: add X (#$ARGUMENTS)`.
- Run the project's tests, linter, and type checks (see CLAUDE.md). Fix failures before moving on.
- If the plan turns out to be wrong, stop and tell me instead of silently changing course.

## 5. Review
- Review your own diff: `git diff dev...HEAD`. Check it against the plan, look for leftover debug code, missing tests, and unrelated changes.
- Comment on the issue with a "## Work done" summary: what changed, any deviations from the plan, follow-ups.

## 6. Pull request
- `git push -u origin HEAD`
- `gh pr create --base dev --title "<concise title>" --body "<summary>\n\nCloses #$ARGUMENTS"`
- GitHub does not link a PR into `dev` to its issue in the Development section. `Closes #N` is ignored for non-default bases, the linked branch doesn't do it, and there is no API for it. Ask me to link the issue by hand from the PR's sidebar: Development → gear → pick the issue.

## 7. Hand off for review
- Do **not** enable auto-merge or merge the PR. Leave it open for my review.
- `dev` is not the default branch, so merging won't close the issue automatically. Remind me to close it after merge (`gh issue close $ARGUMENTS`).

Finish by giving me the PR URL and a one-line status.
