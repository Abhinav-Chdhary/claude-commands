---
description: End-of-session wrap-up — persist remaining plans and a session report
argument-hint: "[optional session label]"
---

Wrap up the current session. Use the current working directory as the root; never hardcode absolute paths.

**Location and naming** — before writing anything, check for a workspace or repo `CLAUDE.md` / `AGENTS.md` that defines where plans and reports live and how they are named, and follow it. Also look at how existing files under `Plans/` and `Reports/` are already organised and match them. Only if there is no such convention, default to `./Plans/<kebab-case-topic>.md` and `./Reports/YYYY-MM-DD-HHMM-<short-slug>.md`.

1. **Plans** — for any work that is incomplete or deferred, write/update a markdown plan file.
   - Include: goal, current status, remaining steps, open questions, relevant files (with `path:line` refs), and any decisions already made.
   - If a plan file for the same topic exists, update it in place rather than creating a duplicate.

2. **Report** — write a session log covering:
   - **Summary** (2–3 sentences): what this session set out to do.
   - **What was done**: concrete changes, with file paths.
   - **What worked**: approaches that landed, and why.
   - **What did not work**: dead ends, failed attempts, root causes if known.
   - **Follow-ups**: links to the Plans files created/updated above.
   - **Commands / verification**: key commands run and their outcomes (build, lint, tests).

3. Create the plans/reports directories if they do not exist. Do not overwrite unrelated existing files. Do not commit anything.

Keep session prompt text in the written files only — never echo the user's verbatim prompts to the console (they may contain sensitive or regulated content).

Session label (optional): $ARGUMENTS
