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

3. **Work-log entry** — append one line to the central journal at `/Users/apple/Documents/MedseeRepos/.context-lab/worklog.jsonl` so future sessions can recall this work (used by `recall.py`). Synthesize the entry from THIS session — you already have the full context, do NOT parse transcripts. Use these exact keys (matching the existing schema):
   - `date`: today (`YYYY-MM-DD`)
   - `branch`: current git branch (`git branch --show-current`), or `null`
   - `title`: short imperative slug of the session's goal (≤60 chars, e.g. `fix-smart-dictation-bugs`)
   - `first_prompt`: the user's original opening ask this session, verbatim and trimmed
   - `files_edited`: array of **repo-relative** paths you created/edited (e.g. `medsee-webapp/src/...`); exclude `Plans/`, `Reports/`, `.claude/`, worktree/placeholder copies
   - `files_read`: array of the few paths *central* to the work — skip incidental reads
   - `source`: `"endit"`

   Append with python (handles JSON escaping; never rewrite the file):
   ```bash
   python3 - <<'PY'
   import json
   entry = {"date": "...", "branch": "...", "title": "...",
            "first_prompt": "...", "files_edited": [...], "files_read": [...],
            "source": "endit"}
   with open("/Users/apple/Documents/MedseeRepos/.context-lab/worklog.jsonl", "a") as f:
       f.write(json.dumps(entry) + "\n")
   PY
   ```
   Keep the prompt/content text in the file only — never echo `first_prompt` to the console (PHI).

4. Create the `Plans/` and `Reports/` directories if they do not exist. Do not overwrite unrelated existing files. Do not commit anything.

Session label (optional): $ARGUMENTS
