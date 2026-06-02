---
description: End-of-session wrap-up — persist remaining plans and a session report
argument-hint: "[optional session label]"
---

Wrap up the current session. Use the current working directory as the root.

1. **Plans** — for any work that is incomplete or deferred, write/update a markdown file under `./Plans/`:
   - Filename: `YYYY-MM-DD-<kebab-case-topic>.md` (today's date).
   - Include: goal, current status, remaining steps, open questions, relevant files (with `path:line` refs), and any decisions already made.
   - If a plan file for the same topic exists, update it in place rather than creating a duplicate.

2. **Report** — write a session log to `./Reports/YYYY-MM-DD-HHMM-<short-slug>.md` covering:
   - **Summary** (2–3 sentences): what this session set out to do.
   - **What was done**: concrete changes, with file paths.
   - **What worked**: approaches that landed, and why.
   - **What did not work**: dead ends, failed attempts, root causes if known.
   - **Follow-ups**: links to the Plans files created/updated above.
   - **Commands / verification**: key commands run and their outcomes (build, lint, tests).

3. Create the `Plans/` and `Reports/` directories if they do not exist. Do not overwrite unrelated existing files. Do not commit anything.

Session label (optional): $ARGUMENTS
