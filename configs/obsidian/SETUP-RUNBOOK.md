tags: #runbook #obsidian
runbook created: 2026-03-18

---

# Runbook Info:

## Name:
Obsidian Vault Setup - darko vault

## Runbook goals:
Replicate Darko's Obsidian vault configuration on a new machine or after a fresh install.

**Current version:** 1.0

Version history:

| Version | Date       | Major change                  |
| ------- | ---------- | ----------------------------- |
| 1.0     | 2026-03-18 | Initial capture of all config |

---
## Useful links:
- Obsidian CLI docs: https://help.obsidian.md/cli
- Obsidian Settings path: `~/workspace/darko/.obsidian/`
- OpenCode skills: `~/.config/opencode/skills/obsidian-*/SKILL.md`

---

# Runbook:

## Prerequisites
- Obsidian installed (AppImage or Flatpak on Linux)
- CLI enabled: Settings > General > Command line interface > Register CLI
- Vault cloned/synced to `~/workspace/darko`
- Obsidian running (CLI is a remote control, needs the app)

## Step 1: Verify CLI works
```bash
obsidian version
obsidian help
```
- Outcome: Version number displayed, help text shown

## Step 2: Configure core plugins via eval
The CLI has no direct settings command, but `eval` can set anything:

### Daily Notes plugin
```bash
obsidian eval vault="darko" code="
const plugin = app.internalPlugins.plugins['daily-notes'];
plugin.instance.options.folder = 'AREAS/inbox';
plugin.instance.options.format = 'YYYY-MM-DD';
plugin.instance.options.template = 'RESOURCES/templates/daily note template';
plugin.saveData(plugin.instance.options);
JSON.stringify(plugin.instance.options);
"
```
- Outcome: `{"folder":"AREAS/inbox","format":"YYYY-MM-DD","template":"RESOURCES/templates/daily note template"}`

### Templates plugin
```bash
obsidian eval vault="darko" code="
const plugin = app.internalPlugins.plugins['templates'];
plugin.instance.options.folder = 'RESOURCES/templates';
plugin.saveData(plugin.instance.options);
JSON.stringify(plugin.instance.options);
"
```
- Outcome: `{"folder":"RESOURCES/templates"}`

## Step 3: Verify configs written
```bash
cat ~/workspace/darko/.obsidian/daily-notes.json
cat ~/workspace/darko/.obsidian/templates.json
```
- Outcome: Both files exist with correct values

## Step 4: Verify daily note works
```bash
obsidian daily:path vault="darko"
```
- Outcome: `AREAS/inbox/YYYY-MM-DD.md`

## Step 5: Verify template creation works
```bash
obsidian create vault="darko" path="AREAS/inbox/test-template" template="homelab log"
obsidian read vault="darko" path="AREAS/inbox/test-template"
obsidian delete vault="darko" path="AREAS/inbox/test-template.md"
```
- Outcome: Note created with template content, then cleaned up

---

# Reference: Expected Config State

## app.json
```json
{
  "alwaysUpdateLinks": true,
  "vimMode": true,
  "attachmentFolderPath": "./resources"
}
```

## core-plugins.json (key plugins)
```
daily-notes: true
templates: true
sync: true
bases: true
graph: true
backlink: true
outgoing-link: true
tag-pane: true
```

## daily-notes.json
```json
{
  "folder": "AREAS/inbox",
  "format": "YYYY-MM-DD",
  "template": "RESOURCES/templates/daily note template"
}
```

## templates.json
```json
{
  "folder": "RESOURCES/templates"
}
```

## Community plugins
```
templater-obsidian
obsidian-minimal-settings
obsidian-local-rest-api
mermaid-tools
```

---

# Vault Templates (9 total)
Located in `RESOURCES/templates/`:
1. daily note template
2. people
3. main note template
4. project main template
5. retro tech project template
6. runbook template
7. bow episode template
8. product feedback
9. homelab log

---

# CLI Gotchas (Linux)
- Obsidian process is `electron39`, not `obsidian`. Check with: `pgrep -f 'obsidian/app.asar'`
- Use `path=` not `name=` when creating notes with `/` in the path
- `obsidian daily` creates notes at vault root if daily-notes.json is not configured
- `template=` fails silently if templates.json folder is not set