---
description: Open a PR, drive it to a clean Greptile review, then merge and clean up
argument-hint: "[PR title or short description — omit to infer from the commits]"
allowed-tools: Bash(gh pr create:*), Bash(gh pr view:*), Bash(gh pr diff:*), Bash(gh pr merge:*), Bash(gh pr comment:*), Bash(gh pr checks:*), Bash(gh api:*), Bash(gh repo view:*), Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git branch:*), Bash(git push:*), Bash(git fetch:*), Bash(git rev-parse:*), Bash(git symbolic-ref:*), Bash(git worktree:*), Read, Grep, Glob, Edit, Write
---

Take the current work from branch to merged, gated on a clean Greptile review. Context: **$ARGUMENTS**

Hard rule, above everything below: **if a product decision is needed, STOP and ask.** See §6.

## 1. Open the PR

- Confirm the branch is pushed and has commits the base branch doesn't.
- Find the real default branch — `git symbolic-ref refs/remotes/origin/HEAD` — and don't assume `main`. Repos that deploy from `main` often take PRs into `staging` or `develop`.
- Write the body in the house order: **TLDR**, then what each commit impacts, then breaking changes. Include how you verified it locally and which areas are affected. If the repo's `CLAUDE.md` specifies a different order, that wins.
- `gh pr create --base <default> --head <branch>`.

If a PR already exists for this branch, skip to §2 and say so.

## 2. Request and read the review

Request it the way the repo expects — usually a comment containing `@greptileai`.

Then read **both** places, because they disagree:

- **The PR description.** Greptile appends a `<!-- greptile_comment -->` block with `Greptile Summary`, `Confidence Score: N/5`, and `Files Needing Attention`. **The score only ever lives here**, never in comments.
  `gh pr view <n> --json body --jq '.body'`
- **Inline review comments** — the actual findings, tagged P0/P1/P2.
  `gh api repos/{owner}/{repo}/pulls/<n>/comments`
- **Review summaries and conversation comments.**
  `gh api repos/{owner}/{repo}/pulls/<n>/reviews` and `gh pr view <n> --comments`

### Check the score is not stale before acting on it

This is the trap. The description block carries `Last reviewed commit: [...]`. Compare it to `gh api repos/{owner}/{repo}/pulls/<n> --jq '.head.sha'`.

**If they differ, the score and its stated reasons describe code that no longer exists.** Do not "fix" findings it lists — they may already be fixed. Say so plainly, with the commit it actually scored, and re-request a review.

Two behaviours to expect:

- An `@greptileai` mention often produces only a **comment reply**, which does *not* refresh the score block. Only a full review run does.
- A comment saying "no remaining merge-blocking findings" is a different statement from the numeric score. Report both; never paraphrase one as the other.

## 3. Fix what's real

For each finding: decide whether it is valid, then fix it or say why not. Don't fix something just because a bot said it.

- **Verify every fix empirically.** Run the thing. Exercise the predicate against real data. `bash -n`, a dry run, a unit test — whatever proves it, rather than reading the diff and declaring it correct.
- **Prefer removing the ambiguity over adding a check.** If a name can collide, make collisions impossible rather than detecting them.
- **Re-read your own change afterwards.** Restructuring to fix one finding routinely introduces another; a fix that moves a guard inside a branch is the classic. Findings you introduce and catch yourself still belong in the PR comment.
- Reply on the PR saying what changed and how it was verified, then push and re-request.

## 4. Iterate

Repeat §2–§3 **until the score is 5/5**, or until **5 iterations**, whichever comes first.

Count an iteration as one review-and-fix round. At the cap, stop and report: the current score, every finding and its status, and what you'd do next. Don't keep going silently.

**Never push a cosmetic or empty commit to bait a review run.** If the score is stale and mentions won't refresh it, say so and let the user hit the re-trigger link in the description block. Churning the branch to move a number is worse than the number being wrong.

If the score plateaus below 5/5 with **no new substantive finding** — every specific objection resolved and the remaining gap is the reviewer's judgement about inherent risk — stop and tell the user that plainly. Let them decide whether to merge. Don't keep editing working code to chase a number.

## 5. Merge and clean up

Only after §4 ends in 5/5, or the user says to merge anyway.

- Check `gh pr view <n> --json mergeable,mergeStateStatus,statusCheckRollup` — CI green, state clean.
- Match the repo's merge style: `git log <base> --merges --oneline -5`. Merge commits and squashes leave different histories; follow whichever the repo already uses.
- `gh pr merge <n>` with that method. Confirm `state=MERGED` and the merge commit landed on the base.
- Delete the branch locally and on origin.
- If the work was in a worktree, `git worktree remove` it, then `git worktree prune`, and remove the parent directory if now empty.
- If the repo's workspace keeps plans/reports/TODOs, update them. State plainly what is merged versus what has actually been *run* — merging a deploy script changes no infrastructure, and that difference is what someone will get wrong later.

## 6. When to STOP

Stop immediately, report, and wait — do not guess:

- **Any product decision.** What a feature should do, which behaviour is correct, a user-visible tradeoff, naming that implies a contract, anything affecting cost or data retention.
- A finding that is only fixable by changing intended behaviour rather than the implementation.
- Scope creep: the fix needs changes outside what this PR set out to do.
- Anything irreversible or outward-facing beyond the PR itself — force-push, a production change, deleting a remote resource, touching another repo.
- The base branch or merge target is ambiguous.

When stopping, give: what you were doing, the specific decision needed, the options with a recommendation, and what's already done so the user can resume.

## Reporting

Keep it short. Per iteration: the score, whether it was stale, each finding with a one-line verdict, and what you changed. At the end: merged or not, the merge commit, what was cleaned up, and anything left to run.
