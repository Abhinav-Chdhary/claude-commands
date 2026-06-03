# claude-commands

A small collection of custom [Claude Code](https://docs.anthropic.com/en/docs/claude-code) slash commands I use day to day.

## Commands

| Command | Description |
|---------|-------------|
| [`/endit`](commands/endit.md) | End-of-session wrap-up — persists deferred plans to `Plans/` and a session log to `Reports/` in the current working directory. |
| [`/reviewinator`](commands/reviewinator.md) | Reviews your changes (last commit / uncommitted / PR diff), rates them out of 5, flags P0/P1/P2 vulnerabilities, and writes a self-contained HTML report to `Reviews/`. |
| [`/standup`](commands/standup.md) | Summarizes today's work from git history, filtered/grouped by org. Pass the org name, or omit it and get asked which of the discovered orgs to summarize. |

## Install

**Prerequisite:** [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed.

### Option A — plugin (recommended)

This repo is a Claude Code [plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces.md).
Install everything with two commands inside any Claude Code session — no cloning, no
symlinks, and `/plugin marketplace update` keeps it current:

```text
/plugin marketplace add Abhinav-Chdhary/claude-commands
/plugin install claude-commands@claude-commands
```

That's it — `/endit`, `/reviewinator`, and `/standup` are now available everywhere.

---

The options below are the manual route if you'd rather not use the plugin system.
Claude Code reads slash commands from Markdown files in a `commands/` directory —
`~/.claude/commands/` for personal (user-scope) commands available in **every**
session, or a project's `.claude/commands/` for commands scoped to **one** repo.

### Option B — symlink

Links the files so a `git pull` here instantly updates your installed commands.

```bash
# 1. Clone this repo anywhere you like
git clone https://github.com/Abhinav-Chdhary/claude-commands.git
cd claude-commands

# 2. Make sure your user commands directory exists
mkdir -p ~/.claude/commands

# 3. Symlink every command into it (run from the repo root)
for f in commands/*.md; do
  ln -sf "$PWD/$f" ~/.claude/commands/"$(basename "$f")"
done
```

### Option C — copy

Simpler, but you'll need to re-copy to pick up future updates.

```bash
mkdir -p ~/.claude/commands
cp commands/*.md ~/.claude/commands/
```

### Verify

Open a new Claude Code session (or run `/reload-commands` in an existing one) and
type `/` — you should see `/endit`, `/reviewinator`, and `/standup` in the list.

### Update later

```bash
cd claude-commands && git pull   # symlink installs are now up to date automatically
# (copy installs: re-run the cp command above)
```

### Project scope instead

To make a command available only inside one project, drop the `.md` file in that
project's `.claude/commands/` directory instead of `~/.claude/commands/`.

## Usage

```text
/reviewinator uncommitted changes
/reviewinator last committed change
/reviewinator pr changes in current branch

/endit
/endit my-feature-session

/standup                 # discovers orgs and asks which to summarize
/standup Abhinav-Chdhary # summarize today's work for a specific org
/standup all             # every repo, regardless of org
```

## License

[MIT](LICENSE)
