# AI Skills, Configs & Tools

My collection of agent skills, configuration files, and tooling for AI coding assistants (OpenCode, Claude Code, Kiro, etc.).

## Structure

```
skills/              Agent skills (SKILL.md format, compatible with OpenCode/Claude Code/Codex)
  obsidian-daily/    Daily note management via Obsidian CLI
  obsidian-vault/    Vault operations, search, templates, health checks
  obsidian-capture/  Quick capture & smart routing to the right note

rules/               Always-loaded rules files for agent context
  obsidian.md        Vault awareness + cardinal rules + skill pointers

configs/             Application configuration snapshots
  obsidian/          Obsidian vault .obsidian/ config files
```

## Installation

### Skills

Skills are loaded from `~/.config/opencode/skills/` (OpenCode) or `~/.claude/skills/` (Claude Code) or `~/.agents/skills/` (universal).

Symlink them to your preferred location:

```bash
# Kiro
ln -sf $(pwd)/skills/obsidian-daily ~/.kiro/skills/obsidian-daily
ln -sf $(pwd)/skills/obsidian-vault ~/.kiro/skills/obsidian-vault
ln -sf $(pwd)/skills/obsidian-capture ~/.kiro/skills/obsidian-capture
# OpenCode
ln -sf $(pwd)/skills/obsidian-daily ~/.config/opencode/skills/obsidian-daily
ln -sf $(pwd)/skills/obsidian-vault ~/.config/opencode/skills/obsidian-vault
ln -sf $(pwd)/skills/obsidian-capture ~/.config/opencode/skills/obsidian-capture

# Or universal (.agents)
ln -sf $(pwd)/skills/obsidian-daily ~/.agents/skills/obsidian-daily
ln -sf $(pwd)/skills/obsidian-vault ~/.agents/skills/obsidian-vault
ln -sf $(pwd)/skills/obsidian-capture ~/.agents/skills/obsidian-capture
```

### Rules

```bash
ln -sf $(pwd)/rules/obsidian.md ~/.kiro/rules/obsidian.md
```

### Obsidian Config

The `configs/obsidian/` directory contains config snapshots. To apply them to a vault:

```bash
cp configs/obsidian/*.json ~/workspace/darko/.obsidian/
```

Or use the Obsidian CLI `eval` command to configure programmatically - see [`configs/obsidian/SETUP-RUNBOOK.md`](configs/obsidian/SETUP-RUNBOOK.md) for the full step-by-step.

## Requirements

- [Obsidian](https://obsidian.md) with CLI enabled (Settings > General > CLI)
- Obsidian must be running (CLI is a remote control, not headless)
- Linux: Process detection uses `pgrep -f 'obsidian/app.asar'` (Electron app)

## Skills Overview

| Skill | Trigger | What It Does |
|-------|---------|--------------|
| `obsidian-daily` | TODOs, wins, boos, daily notes, pro tips | Daily note CRUD, append-only, pro tip dual-routing |
| `obsidian-vault` | Search, find, move, tags, properties, health | Full vault operations, 9 templates registered |
| `obsidian-capture` | "note this", "save this", meeting notes, feedback | Smart routing: daily note vs new note vs existing note |
