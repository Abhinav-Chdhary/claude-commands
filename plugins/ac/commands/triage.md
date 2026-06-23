---
description: Check review comments on one or more PRs, triage them by severity, and suggest fixes
argument-hint: "[PR numbers or URLs — omit to use the current branch's PR]"
allowed-tools: Bash(gh pr view:*), Bash(gh pr diff:*), Bash(gh pr list:*), Bash(gh api:*), Bash(gh repo view:*), Bash(git branch:*), Bash(git rev-parse:*), Bash(git status:*), Bash(git diff:*), Bash(git log:*), Read, Grep, Glob, Edit, Write
---

Check the review comments on the requested PR(s), triage them, and suggest fixes. PR(s): **$ARGUMENTS**

## 1. Resolve which PRs to inspect

- If `$ARGUMENTS` is non-empty, treat each token as a PR reference — a number (`123`), a `owner/repo#123`, or a full GitHub URL. Resolve each one.
- If `$ARGUMENTS` is empty, find the PR for the **current branch**: `gh pr view --json number,url,title` (this resolves the branch's PR automatically). If there is no PR for the current branch, say so and offer to list open PRs with `gh pr list`.
- Establish the repo context with `gh repo view --json nameWithOwner` so the API calls below target the right `owner/repo`.

## 2. Gather the review feedback for each PR

For every resolved PR (`<n>`), collect **all** sources of feedback — don't rely on just one:

- **Inline diff comments** (the ones attached to specific lines):
  `gh api repos/{owner}/{repo}/pulls/<n>/comments --paginate` — capture `path`, `line`/`original_line`, `body`, `user.login`, and whether it's part of a resolved thread.
- **Review summaries / verdicts** (approve, request-changes, overall notes):
  `gh api repos/{owner}/{repo}/pulls/<n>/reviews --paginate` — capture `state` and `body`.
- **Conversation comments** (general PR thread, not tied to a line):
  `gh pr view <n> --comments`.
- Pull the diff for context so suggestions reference real code: `gh pr diff <n>`. Read the actual files (`Read`/`Grep`) when a comment needs more surrounding context than the diff shows.

Skip comments that are clearly already resolved or are your own bot/CI noise unless they flag a real problem. Note if a PR has zero outstanding review comments — that's a valid, useful result.

## 3. Triage

Categorize every actionable comment by severity (mirror the `/ac:reviewinator` tiers so they feel consistent):

- **P0** — must fix before merge: correctness bugs, security issues, broken logic, data loss.
- **P1** — should fix: missing validation, edge cases, unhandled errors, API/contract concerns.
- **P2** — nice to have: style, naming, minor refactors, docs/comments.
- **Non-actionable** — questions, praise, already-addressed, or out-of-scope. List briefly; no fix needed.

Ground every item strictly in an actual review comment — do not invent feedback. Reference the commenter, file, and line.

## 4. Suggest fixes

Output a concise markdown report directly in your response (do **not** write a file):

- **TLDR** at the top: per-PR count of comments by severity and a one-line "what needs the most attention".
- **Per PR**, grouped by severity (P0 → P1 → P2): for each comment give
  - the source: `file:line` (or "conversation") + commenter,
  - a one-line summary of what they asked,
  - **Suggested fix**: a specific, concrete change — show a small code snippet or exact edit where it helps. If a comment is ambiguous or you disagree, say so and explain rather than forcing a fix.
- End the **Non-actionable** items as a short bulleted list with a one-line disposition each.

Keep it tight and skimmable — this is a triage, not an essay. Scale length to the number of comments.

## 5. Offer to apply

After the report, **ask whether to apply the fixes** (use the AskUserQuestion tool). Do not edit any files until I confirm. If I say yes:

- Apply the agreed fixes with `Edit`/`Write`, smallest-diff-first, P0s before P1s.
- After editing, summarize what you changed (`file:line`) and remind me to review, commit, and push — and that I can reply to/resolve the threads on GitHub myself.
- Do **not** commit, push, or resolve review threads automatically unless I explicitly ask.
