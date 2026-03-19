---
name: obsidian-vault
description: Search, read, organize, and maintain Darko's Obsidian vault. Use when finding notes, searching content, moving or renaming files, managing tags or properties, checking vault health (orphans, broken links), or navigating the vault structure. Covers PARA organization, file operations, tag management, and vault health checks.
---

# Obsidian Vault Operations

This skill handles vault-wide operations in Darko's Obsidian vault using the Obsidian CLI.

## Pre-Flight Check

Before ANY Obsidian CLI operation, verify Obsidian is running:

```bash
pgrep -f 'obsidian/app.asar' > /dev/null 2>&1 || echo "OBSIDIAN_NOT_RUNNING"
```

Note: On Linux, Obsidian runs as an Electron app - the process name is `electron`, not `obsidian`. This check greps for the asar path instead.

If Obsidian is not running, tell Darko:
> "Obsidian isn't running. Start it up and I'll proceed."

Do NOT attempt to launch Obsidian yourself. Wait for confirmation.

## Vault Context

- **Vault name:** darko
- **Vault path:** ~/workspace/darko
- **Organization:** PARA system

### PARA Folder Structure
```
PROJECTS/       - Active projects with clear goals and deadlines
AREAS/          - Ongoing areas of responsibility
  inbox/        - Daily notes (YYYY-MM-DD.md)
  inbox/resources/  - Inbox-related resources
RESOURCES/      - Reference material, knowledge base
  protips.md    - Collated pro tips
  people/       - Notes about people
  templates/    - Note templates
  writing-voice.md
  documentation/
  zettelkasten/
  wiki/
ARCHIVE/        - Completed or inactive items
```

## Reading Notes

### Read a specific note
```bash
obsidian read vault="darko" file="RESOURCES/protips"
```

Note: File paths are relative to vault root. Omit the `.md` extension.

### Read with metadata
```bash
obsidian file vault="darko" file="RESOURCES/protips"
```

### List files in the vault
```bash
obsidian files vault="darko"
```

### View folder structure
```bash
obsidian folders vault="darko"
```

### List files in a specific folder
```bash
obsidian files vault="darko" folder="PROJECTS"
```

## Searching

### Full-text search
```bash
obsidian search vault="darko" query="strands agents"
```

### Search with surrounding context (grep-like)
```bash
obsidian search:context vault="darko" query="AgentCore" limit=10
```

### Search with JSON output (for processing)
```bash
obsidian search vault="darko" query="TODO" format=json
```

## Vault Templates

Templates live in `RESOURCES/templates/` inside the vault. The CLI uses them natively via `template="<name>"`. Do NOT duplicate template content in skills - always use the CLI's template parameter so Darko's vault stays the single source of truth.

### Available Templates

| Template Name (for CLI) | When to Use | Creates In |
|---|---|---|
| `daily note template` | Daily notes (auto-created by Obsidian) | `AREAS/inbox/` |
| `people` | New person entry | `RESOURCES/people/` |
| `main note template` | General-purpose note | Varies |
| `project main template` | New project kickoff | `PROJECTS/` |
| `retro tech project template` | Vintage/retro computing project | `PROJECTS/` |
| `runbook template` | Operational runbook | `RESOURCES/runbooks/` |
| `bow episode template` | Build On Weekly episode planning | `PROJECTS/` |
| `product feedback` | Bug report or feature request for a product | `AREAS/inbox/` (daily) or standalone |
| `homelab log` | Homelab setup, fix, config change, or node build | `AREAS/inbox/` (daily) or standalone |

### Template Key Details

**Daily note template** - Uses inline frontmatter (NOT YAML): `tags:`, `associations:`, `when:` fields, then TODO sections (URGENT/Important/Easy), WINS, BOOS, Notes.

**People template** - Has `tags:`, `date_created:`, `name:`, `email:` fields, then a Notes section.

**Project template** - Has `tags: #project`, project info block (Name, Codename, Stakeholders, Key Dates, Links), TODO sections, Meetings, Notes.

**Runbook template** - Has version history table, useful links, numbered steps with outcomes.

**Product feedback template** - Has `tags: #feedback #product`, `product:`, `date_reported:` fields, then Title, Severity, Component, Description, Steps to Reproduce, Expected Behavior, Why This Matters, Suggested Implementation, Screenshot.

**Homelab log template** - Has `tags: #homelab`, `host:`, `ip:`, `date:` fields, then Context, What was done, Configuration/Commands (code block), Key learnings, Next steps.

## File Operations

### Create a new note
```bash
obsidian create vault="darko" path="PROJECTS/new-project" content="# New Project\n\nDescription here."
```

Note: Use `path=` (not `name=`) when the path contains `/` separators. `name=` only works for notes in the vault root.

### Create from template
```bash
obsidian create vault="darko" path="RESOURCES/people/jane-doe" template="people"
obsidian create vault="darko" path="PROJECTS/new-project" template="project main template"
obsidian create vault="darko" path="RESOURCES/runbooks/deploy-process" template="runbook template"
obsidian create vault="darko" path="AREAS/inbox/kiro-feedback-title" template="product feedback"
obsidian create vault="darko" path="AREAS/inbox/triglav-zfs-setup" template="homelab log"
```

Note: Template names must match the filename in `RESOURCES/templates/` exactly (without `.md`), including spaces. Template support requires the template folder to be configured in Obsidian Settings > Templates. If not configured, use `content=` with inline content instead.

### Move a note (wikilinks update automatically)
```bash
obsidian move vault="darko" file="AREAS/inbox/old-note" to="ARCHIVE/"
```

This is one of the biggest advantages over raw file operations - the CLI rewrites all wikilinks across the vault when you move a file.

### Delete a note (moves to trash)
```bash
obsidian delete vault="darko" file="ARCHIVE/old-note"
```

### Append to any note
```bash
obsidian append vault="darko" file="RESOURCES/protips" content="\n\n### New Tip\nContent here."
```

### Prepend to a note (after frontmatter)
```bash
obsidian prepend vault="darko" file="some-note" content="Inserted content"
```

## Properties (Frontmatter)

### View a note's properties
```bash
obsidian properties vault="darko" file="some-note"
```

### Set a property
```bash
obsidian property:set vault="darko" file="some-note" name="status" value="active"
```

### Remove a property
```bash
obsidian property:remove vault="darko" file="some-note" name="status"
```

## Tag Management

### List all tags in the vault
```bash
obsidian tags vault="darko"
```

### List files with a specific tag
```bash
obsidian tag vault="darko" tag="#aws"
```

### Bulk rename a tag across the entire vault
```bash
obsidian tags:rename vault="darko" old=oldtag new=newtag
```

## Vault Health

### Find orphan notes (nothing links to them)
```bash
obsidian orphans vault="darko"
```

### Find unresolved links (links pointing to non-existent notes)
```bash
obsidian unresolved vault="darko"
```

### Check outgoing links from a note
```bash
obsidian links vault="darko" file="some-note"
```

### Check backlinks to a note
```bash
obsidian backlinks vault="darko" file="some-note"
```

## Patching / Replacing Content

The Obsidian CLI has no `patch` or `replace` command. For in-place text edits (fixing typos, expanding a section, replacing a thin entry with a richer one), use `fs_write str_replace` directly on the vault file.

### Vault file path pattern
`~/workspace/darko/<note-path>.md`

Examples:
- Daily note: `~/workspace/darko/AREAS/inbox/2026-03-18.md`
- Project note: `~/workspace/darko/PROJECTS/photon.md`
- Person note: `~/workspace/darko/RESOURCES/people/jane-doe.md`

### Patch workflow
1. **Read the note first** via `obsidian read vault="darko" file="<path>"` to get current content
2. Use `fs_write str_replace` with enough context in `old_str` to match uniquely
3. Confirm the change to Darko

### Rules for patching
- **Never use patching to delete content** — only to replace or expand existing content
- **For structural operations** (move, rename, delete), always use the CLI — those need wikilink updates
- **For adding new content**, prefer `append`/`prepend` via CLI — those go through Obsidian's API
- After patching, the Obsidian index may take a moment to catch up — this is fine for content edits

## Critical Rules

1. **NEVER delete content** from notes unless Darko explicitly asks. Only append.
2. **Use `move` instead of creating + deleting** - move preserves wikilinks.
3. **Always specify `vault="darko"`** in every command.
4. **Respect PARA structure** - suggest the right folder when creating notes.
5. **Tags go inline in content**, never in markdown headers.
6. **Read before modifying** - always check current state before appending or setting properties.

## Output Formats

Many commands support format options:

| Format | Use Case |
|--------|----------|
| `json` | Pipe through jq, programmatic processing |
| `csv` / `tsv` | Spreadsheet export |
| `md` | Markdown format |
| `paths` | File paths only (for piping) |
| `text` | Human-readable (default) |
| `tree` | Folder hierarchy |
| `yaml` | YAML format (default for properties) |

Example:
```bash
obsidian search vault="darko" query="TODO" format=json
```

## Bulk Operations

For operations across many files, pipe commands together:

```bash
obsidian tag vault="darko" tag="#archive" | while read -r note; do
  obsidian move vault="darko" file="$note" to="ARCHIVE/"
done
```

Note: For truly massive bulk operations (3000+ files), the CLI will be slow due to per-command IPC overhead. In those rare cases, suggest a Python script operating on the markdown files directly.
