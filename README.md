# claude-commands

A small collection of custom [Claude Code](https://docs.anthropic.com/en/docs/claude-code) slash commands I use day to day.

## Commands

| Command | Description |
|---------|-------------|
| [`/ac:endit`](plugins/ac/commands/endit.md) | End-of-session wrap-up — persists deferred plans to `Plans/` and a session log to `Reports/` in the current working directory. |
| [`/ac:reviewinator`](plugins/ac/commands/reviewinator.md) | Reviews your changes (last commit / uncommitted / PR diff), rates them out of 5, flags P0/P1/P2 vulnerabilities, and writes a self-contained HTML report to `Reviews/`. |
| [`/ac:standup`](plugins/ac/commands/standup.md) | Summarizes today's work from git history, filtered/grouped by org. Pass the org name, or omit it and get asked which of the discovered orgs to summarize. |
| [`/ac:triage`](plugins/ac/commands/triage.md) | Checks review comments on one or more PRs (pass PR numbers/URLs, or omit to use the current branch's PR), triages them P0/P1/P2, suggests concrete fixes, then offers to apply them. |

All commands ship in a single plugin named `ac`, so they share the `/ac:` prefix once installed.

## Install

**Prerequisite:** [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed.

**Quickest route — just ask Claude.** Prompt your Claude to install like this => 
```text
install the ac plugin from the Abhinav-Chdhary/claude-commands marketplace
```

### Option A — plugin (recommended)

This repo is a Claude Code [plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces.md).
Add it once inside any Claude Code session — no cloning, no symlinks — then install the
`ac` plugin. `/plugin marketplace update` keeps it current.

```text
# 1. Add the marketplace (one time)
/plugin marketplace add Abhinav-Chdhary/claude-commands

# 2. Install the plugin (all three commands)
/plugin install ac@claude-commands
```

All commands land under the `/ac:` prefix — `/ac:standup`, `/ac:reviewinator`, `/ac:endit`.
Prefer a menu? Run `/plugin` and browse the `claude-commands` marketplace interactively.

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
for f in plugins/*/commands/*.md; do
  ln -sf "$PWD/$f" ~/.claude/commands/"$(basename "$f")"
done
```

To install just one, link only its file, e.g.
`ln -sf "$PWD/plugins/ac/commands/standup.md" ~/.claude/commands/standup.md`.
(Manual installs land as bare `/standup` etc. — the `/ac:` prefix only applies to the plugin route above.)

### Option C — copy

Simpler, but you'll need to re-copy to pick up future updates.

```bash
mkdir -p ~/.claude/commands
cp plugins/*/commands/*.md ~/.claude/commands/
```

### Verify

Open a new Claude Code session (or run `/reload-commands` in an existing one) and
type `/` — you should see the commands in the list (`/ac:…` via the plugin, or bare
`/endit`, `/reviewinator`, `/standup` via the manual routes).

### Update later

```bash
cd claude-commands && git pull   # symlink installs are now up to date automatically
# (copy installs: re-run the cp command above)
```

### Project scope instead

To make a command available only inside one project, drop the `.md` file in that
project's `.claude/commands/` directory instead of `~/.claude/commands/`.

## Usage

Plugin installs use the `/ac:` prefix (drop it for manual installs):

```text
/ac:reviewinator uncommitted changes
/ac:reviewinator last committed change
/ac:reviewinator pr changes in current branch

/ac:endit
/ac:endit my-feature-session

/ac:standup                 # discovers orgs and asks which to summarize
/ac:standup Abhinav-Chdhary # summarize today's work for a specific org
/ac:standup all             # every repo, regardless of org

/ac:triage                  # triage the current branch's PR
/ac:triage 123 124          # triage two specific PRs by number
/ac:triage https://github.com/org/repo/pull/123
```

## License

[MIT](LICENSE)
