---
name: obsidian-daily
description: Manage Darko's Obsidian daily notes. Use when adding TODOs, wins, boos, notes, meeting notes, pro tips, or any content to today's daily note. Also use when reading or checking today's note. Covers the daily note template, append-only rules, inline tag conventions, and pro tip routing.
---

# Obsidian Daily Note Management

This skill handles all daily note operations in Darko's Obsidian vault using the Obsidian CLI.

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
- **Daily notes location:** `AREAS/inbox/YYYY-MM-DD.md`
- **Date format:** `YYYY-MM-DD` (e.g., `2026-03-18`)

## Daily Note Template

Daily notes use **inline frontmatter** (NOT YAML frontmatter blocks). This is critical - Obsidian renders these as properties:

```
tags: #daily, 
associations: 
when: YYYY-MM-DD

---
## Daily TODO:

### URGENT:
- [ ] 
### Important:
- [ ] 
### Easy:
- [ ] 
---
## WINS:
- 

## BOOS:
- 

---
## Notes:
```

## Core Commands

### Read today's daily note
```bash
obsidian daily:read vault="darko"
```

### Append content to today's daily note
```bash
obsidian daily:append vault="darko" content="<content here>"
```

### Open today's daily note (creates if missing)
```bash
obsidian daily vault="darko"
```

### Read a specific day's note
```bash
obsidian read vault="darko" file="AREAS/inbox/2026-03-18"
```

## Critical Rules

1. **NEVER delete content** from a daily note. Only append. If Darko explicitly asks to remove something, confirm first.
2. **NEVER overwrite** a daily note. Always use `daily:append` or `append`.
3. **Always read first** before appending - check what's already there to avoid duplicates.
4. **Always add inline tags** to content you append (e.g., `#rust`, `#aws`, `#agents`, `#kiro`, `#video`, `#feedback`).
5. **NEVER put tags in markdown headers** - `### My Section #aws` renders badly. Put tags on the line below the header instead.

## Content Formatting

When appending to the Notes section, format content as:

```markdown

### Section Title
#relevant-tag #another-tag

Content goes here with proper markdown formatting.
- Bullet points for lists
- **Bold** for emphasis
- `code` for technical terms
```

Always start appended content with a blank line to separate from existing content.

## Pro Tips Routing

When a conversation produces a pro tip (a reusable insight, shortcut, or technique):

1. **Append to today's daily note** with the `#protips` tag
2. **Also append to the collated protips file:**
```bash
obsidian append vault="darko" file="RESOURCES/protips" content="<protip content>"
```

Format pro tips consistently:
```markdown
### [Brief Title] (YYYY-MM-DD)
#protips #relevant-topic

The actual tip content here.
```

## Adding TODOs

When Darko asks to add a TODO, append it to the daily note. Don't try to insert it into the specific TODO section - just append it clearly:

```bash
obsidian daily:append vault="darko" content="

### TODO Added
- [ ] The task description #relevant-tag"
```

## Adding Wins and Boos

Same approach - append with clear labeling:

```bash
obsidian daily:append vault="darko" content="

### Win
- Description of the win #relevant-tag"
```

## Multi-line Content

For longer content, use the CLI with proper escaping. Newlines in the `content=` parameter work:

```bash
obsidian daily:append vault="darko" content="

### Meeting Notes - Topic Name
#meeting #relevant-tag

**Attendees:** Names
**Key Points:**
- Point one
- Point two

**Action Items:**
- [ ] Action item one
- [ ] Action item two"
```

## Output Formats

When reading notes, you can request different formats:
```bash
obsidian daily:read vault="darko" format=json
obsidian daily:read vault="darko" format=md
```

Default is text, which is fine for most operations.
