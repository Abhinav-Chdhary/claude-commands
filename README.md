# claude-commands

A small collection of custom [Claude Code](https://docs.anthropic.com/en/docs/claude-code) slash commands I use day to day.

## Commands

| Command | Description |
|---------|-------------|
| [`/endit`](commands/endit.md) | End-of-session wrap-up — persists deferred plans to `Plans/` and a session log to `Reports/` in the current working directory. |
| [`/reviewinator`](commands/reviewinator.md) | Reviews your changes (last commit / uncommitted / PR diff), rates them out of 5, flags P0/P1/P2 vulnerabilities, and writes a self-contained HTML report to `Reviews/`. |

## Install

Clone anywhere, then symlink the commands into your Claude Code commands directory:

```bash
git clone https://github.com/Abhinav-Chdhary/claude-commands.git
cd claude-commands

# Link into your personal commands folder (user scope)
ln -s "$PWD/commands/endit.md"        ~/.claude/commands/endit.md
ln -s "$PWD/commands/reviewinator.md" ~/.claude/commands/reviewinator.md
```

Or copy them instead of symlinking if you'd rather not track the source:

```bash
cp commands/*.md ~/.claude/commands/
```

Commands are available in any Claude Code session as `/endit` and `/reviewinator`.

### Project scope

To make a command available only inside one project, drop it in that project's
`.claude/commands/` directory instead of `~/.claude/commands/`.

## Usage

```text
/reviewinator uncommitted changes
/reviewinator last committed change
/reviewinator pr changes in current branch

/endit
/endit my-feature-session
```

## License

[MIT](LICENSE)
