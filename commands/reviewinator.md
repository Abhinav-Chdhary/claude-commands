---
description: Rate user changes out of 5 and flag P0/P1/P2 vulnerabilities, output as HTML
argument-hint: last committed change | uncommitted changes | pr changes in current branch
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git show:*), Bash(gh pr diff:*), Bash(gh pr view:*), Bash(open:*), Read, Write
---

Review the following changes: **$ARGUMENTS**

First, determine which diff to inspect based on the request above:
- "last committed change" → `git show HEAD`
- "uncommitted changes" → `git diff HEAD` (plus `git status` for untracked files)
- "pr changes in current branch" → `gh pr diff` (fall back to `git diff main...HEAD` if no PR / gh unavailable)

Then produce a code review with these requirements:

1. **Rate the changes out of 5** (be honest and specific about why).
2. **Flag vulnerabilities** found in the diff, categorized by severity:
   - **P0** — critical/exploitable, must fix before merge (e.g. auth bypass, injection, secrets, RCE).
   - **P1** — serious, should fix soon (e.g. missing validation, unsafe defaults, data leaks).
   - **P2** — minor/hardening (e.g. weak error handling, info disclosure, deprecated APIs).
   - If none exist for a tier, say "None found."
3. **Add a TLDR section at the very top** with the rating and a one-line summary of the most important findings.
4. **Write the entire review as a self-contained HTML file** — clean inline CSS, readable typography, color-coded severity badges (P0 red, P1 orange, P2 yellow). Save it into a `Reviews/` folder in the current working directory, creating the folder if it does not exist. Use a descriptive, timestamped filename: `./Reviews/YYYY-MM-DD-HHMM-<short-slug>.html`. Do not overwrite unrelated existing files.
5. **At the very end of your response, print the exact shell command to open it**, e.g. `open ./Reviews/2026-06-02-1430-auth-refactor.html`.

Keep the analysis grounded strictly in the actual diff — do not invent issues. Reference specific files and line numbers where possible.
