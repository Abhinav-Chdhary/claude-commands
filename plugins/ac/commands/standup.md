---
description: Summarize the last 24 hours of work from git history, grouped/filtered by org
argument-hint: "[org name or remote owner — omit to be asked]"
allowed-tools: Bash(git log:*), Bash(git remote:*), Bash(git config:*), Bash(git rev-parse:*), Bash(git branch:*), Bash(git show:*), Bash(find:*), Bash(ls:*), Bash(pwd:*)
---

Summarize the work I did in the **last 24 hours** from git history. Org filter (optional): **$ARGUMENTS**

## 1. Resolve the org

"Org" means a GitHub/GitLab organization (the owner segment of a remote URL, e.g. `github.com/<org>/<repo>`).

- If `$ARGUMENTS` is non-empty, use it as the org filter (match case-insensitively against the remote owner).
- If `$ARGUMENTS` is empty, **discover the available orgs first, then ask me which one** before producing the summary:
  1. Find git repos under the current working directory (and its immediate sibling repos, since this may be a multi-repo workspace) — look for `.git` directories, max depth 3.
  2. For each repo, read its remote: `git -C <repo> remote get-url origin`, and extract the org/owner segment.
  3. Present the distinct orgs you found as a numbered list and ask me to pick one (or "all"). Use the AskUserQuestion tool. Do not guess.

## 2. Collect the last 24 hours of commits

For each repo belonging to the chosen org (or all repos if I said "all"):

- Determine my identity: `git config user.email` and `git config user.name`.
- List my commits from the last 24 hours:
  `git -C <repo> log --since="24 hours ago" --until="now" --author="<my email or name>" --pretty=format:"%h %ad %s" --date=format:"%a %H:%M" --no-merges`
- If that returns nothing, also try `--all` branches in case work sits on feature branches:
  `git -C <repo> log --all --since="24 hours ago" --author=...` (de-dupe by commit hash).
- For richer detail, you may inspect `git -C <repo> show --stat <hash>` for notable commits to describe what changed.

Use the local timezone. "Last 24 hours" = a rolling window from exactly 24 hours before now (it may span two calendar days).

## 3. Produce the summary

Output a concise standup-style markdown summary directly in your response (do **not** write a file unless I ask):

- **Header**: `🗓️ Work summary — last 24h (<window start> → <now>) — org: <org>`
- **Per repo** (only repos with commits in the window): repo name, branch(es) touched, and a bulleted list of what was accomplished — grouped by theme/feature, not a raw commit dump. Translate commit messages into plain accomplishments.
- **By the numbers**: total commits, repos touched, rough sense of scope (files/areas).
- **TLDR** at the very top: 1–2 sentences on what the last 24 hours amounted to.

Length (tiered — scale to the work, never sprawl):
- **TLDR**: ≤ 30 words (hard limit — this is the skim line).
- **Per repo**: ≤ 3 bullets, ≤ ~15 words each.
- **Overall**: soft cap ~120 words, hard cap ~150. A quiet window should land well under this — don't pad.

Rules:
- Ground everything strictly in actual git history — do not invent work.
- If no commits exist in the last 24 hours in the chosen org, say so plainly and offer to widen the range (e.g. last 3 days, this week).
- Keep it tight and skimmable — this is a standup recap, not a report.
