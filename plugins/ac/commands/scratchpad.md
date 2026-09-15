---
description: Drain a scratchpad inbox — file each note to its durable home by lifetime, delete the debris
argument-hint: "[path to scratchpad — omit for Docs/CentScratchpad.md]"
allowed-tools: Bash(cat:*), Bash(ls:*), Bash(rg:*), Bash(wc:*), Bash(cp:*), Bash(git status:*), Bash(git -C:*), Read, Edit, Write
---

Drain the scratchpad inbox. Target file (optional): **$ARGUMENTS** — if empty, use
`Docs/CentScratchpad.md` relative to the workspace root.

## 1. Read before touching

Read the scratchpad in full, and read the destinations before writing to them so you append in their
existing voice and structure rather than bolting on a foreign section:

- `Docs/CentFacts.md` — durable facts
- `Docs/CentScale.md` — volume and timing numbers
- `TODO.md` — work in flight and open questions

Back the scratchpad up to your scratchpad directory first. Do not delete anything before the backup exists.

## 2. Classify every entry by lifetime, not topic

Work through the file entry by entry and sort each into exactly one bucket:

- **Durable fact** — still true, still load-bearing, would be retyped into a future conversation.
  → `Docs/CentFacts.md`. Facts only; if it explains *why* a choice was made, put it under standing decisions.
- **Number** — counts, sizes, throughput, timings. → `Docs/CentScale.md`, with the date measured.
- **Work or question** — anything actionable or unresolved. → `TODO.md`.
- **Repo-specific** — belongs with the code it describes. → that repo, in the file that already
  covers it. Check first whether the repo already records it; build pipelines usually do this
  better than a notes file (release hashes, pinned digests, versions).
- **Debris** — credentials, tokens, `tokenSha256`, ObjectIds, signed URLs, shell transcripts,
  one-off command output. → delete. Never file these anywhere, and never echo their values back
  into the conversation.
- **Stale** — was true, now contradicted by the repo or by a newer note. → delete, and say what it
  contradicted.

## 3. Dedupe and flag conflicts, don't silently resolve

- If an entry already exists at the destination, do not add a second copy.
- If an entry **contradicts** the destination or another entry, keep both, mark the conflict inline,
  and surface it. Never pick a winner on the user's behalf.
- If an entry looks done, do not mark it done — ask.

## 4. Verify before you call a fact durable

A fact that has gone stale is worse than one the user retypes. Before writing anything to
`Docs/CentFacts.md`, check it against the repos when it is checkable (a pinned version, a path, a
flag, a hostname). Drop or flag what you cannot confirm.

## 5. Reset the inbox

Replace the scratchpad body with its header stub and update the "Last drained" date. Keep the
header; it is what teaches the next dump where things go.

## 6. Report

Tell me, briefly:

- what moved and where, one line each
- what was deleted as debris or stale, and why
- every conflict you flagged
- anything you could not classify, left in the inbox for me to rule on
- whether `Docs/CentFacts.md` is still about one screen — if it has outgrown that, say which section
  should move into a repo-local guide

Commit the destination files in whichever repos they live in, on the default branch. Do not push.
