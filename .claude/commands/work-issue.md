---
description: Take a GitHub issue from branch to auto-merging PR (explore → plan → execute → review → PR)
argument-hint: <issue-number>
---

You are working on GitHub issue #$ARGUMENTS. Follow these phases in order. Use the `gh` CLI for all GitHub operations.

## 0. Setup
1. Run `gh issue view $ARGUMENTS --comments` and read the issue fully.
2. Make sure the working tree is clean (`git status`). If not, stop and tell me.
3. `git checkout dev && git pull`
4. Create a branch: `git checkout -b issue-$ARGUMENTS-<short-kebab-slug-from-title>`

## 1. Explore
- Find and read the code relevant to the issue. Do NOT edit any files in this phase.
- Note existing patterns, tests, and anything that constrains the solution.

## 2. Plan
- Write a concise plan: goal, files to change, approach, test strategy, risks/open questions.
- Post it on the issue: `gh issue comment $ARGUMENTS --body "<plan>"` with a heading "## Plan".
- **STOP and wait for my approval.** Do not write code until I say go. If I request changes, update the plan (and the issue comment) first.

## 3. Execute
- Implement the approved plan. Keep commits small and focused; reference the issue in each message, e.g. `feat: add X (#$ARGUMENTS)`.
- Run the project's tests, linter, and type checks. Fix failures before moving on.
- If the plan turns out to be wrong, stop and tell me instead of silently changing course.

## 4. Review
- Review your own diff: `git diff dev...HEAD`. Check it against the plan, look for leftover debug code, missing tests, and unrelated changes.
- Comment on the issue with a "## Work done" summary: what changed, any deviations from the plan, follow-ups.

## 5. Pull request
- `git push -u origin HEAD`
- `gh pr create --base dev --title "<concise title>" --body "<summary>\n\nCloses #$ARGUMENTS"`

## 6. Merge
- Enable auto-merge so GitHub merges once required checks pass: `gh pr merge --auto --squash --delete-branch`
- If that fails (auto-merge not enabled for the repo, or no required checks), tell me and leave the PR open for manual review.
- Note: `Closes #$ARGUMENTS` only auto-closes the issue when merging into the default branch. If `dev` is not the default branch, tell me so I can close the issue after merge.

Finish by giving me the PR URL and a one-line status.
