---
name: obsidian-capture
description: Quickly capture and route information to the right place in Darko's Obsidian vault. Use when Darko says note this, save this, capture this, dumps meeting notes, mentions something worth recording, shares feedback, or wants information saved for later. Handles routing decisions - daily note vs new note vs existing note - and formats content with proper tags.
---

# Obsidian Quick Capture & Knowledge Routing

This skill handles capturing information from conversations and routing it to the correct location in Darko's Obsidian vault using the Obsidian CLI.

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
- **Daily notes:** `AREAS/inbox/YYYY-MM-DD.md`
- **Organization:** PARA system

## Routing Decision Tree

When Darko wants to capture information, decide where it goes:

### Route to Today's Daily Note (most common)
Use `obsidian daily:append` for:
- Meeting notes
- Quick thoughts and observations
- TODOs and action items
- Conference/talk notes
- Things that happened today
- Ideas that came up in conversation
- Quick homelab updates (short entries go in daily note, big setups get their own note)
- Quick product feedback (short gripes go in daily note, structured bug reports get their own note)

### Route to an Existing Note (append)
Use `obsidian append` for:
- Pro tips -> `RESOURCES/protips.md` (ALSO add to daily note)
- Information about a known person -> `RESOURCES/people/<name>`
- Updates to an active project -> `PROJECTS/<project-name>`
- Additional content for an existing topic

### Route to a New Note
Use `obsidian create` for:
- A substantial new topic that deserves its own note
- A new project kickoff -> use `template="project main template"`
- A new person entry -> use `template="people"`
- A new runbook -> use `template="runbook template"`
- A structured product bug report / feature request -> use `template="product feedback"`
- A significant homelab setup, build, or fix -> use `template="homelab log"`
- Reference material that will be looked up independently

When creating from templates, use the CLI's native template support:
```bash
obsidian create vault="darko" path="RESOURCES/people/jane-doe" template="people"
obsidian create vault="darko" path="PROJECTS/new-project" template="project main template"
```
Template names must match filenames in `RESOURCES/templates/` exactly (without `.md`), including spaces. Do NOT hardcode template content in this skill - let Obsidian expand the template so Darko's vault stays the source of truth.

**When in doubt, route to the daily note.** It's the inbox. Darko can reorganize later.

## Capture Workflow

### Step 1: Read current daily note
Always check what's already there:
```bash
obsidian daily:read vault="darko"
```

### Step 2: Determine the route
Apply the routing decision tree above.

### Step 3: Format the content
Structure the content with:
- A clear `### heading` for the section
- Inline tags on the line below the heading (never in the heading itself)
- Proper markdown formatting
- Bold for key terms, names, dates
- Bullet points for lists
- Checkboxes for action items

### Step 4: Append the content
```bash
obsidian daily:append vault="darko" content="<formatted content>"
```

Or for non-daily-note destinations:
```bash
obsidian append vault="darko" file="<path>" content="<formatted content>"
```

### Step 5: Confirm what was captured
Tell Darko what was saved and where.

## Content Formatting Templates

### Meeting Notes
```markdown

### Meeting: [Topic/Name] - YYYY-MM-DD
#meeting #relevant-tags

**Attendees:** Name1, Name2
**Context:** Brief context

**Key Points:**
- Point one
- Point two

**Decisions:**
- Decision made

**Action Items:**
- [ ] Action for someone
- [ ] Another action
```

### Product/Tool Feedback

**For structured bug reports** (Steps to Reproduce, Expected Behavior, etc.), create a standalone note from template:
```bash
obsidian create vault="darko" path="AREAS/inbox/kiro-feedback-brief-title" template="product feedback"
```
Then fill in the fields with `property:set` and append the body content.

**For quick feedback** (a gripe, a suggestion, a one-liner), append to the daily note:
```markdown

### [Product] Feedback: [Brief Title]
#feedback #product-name

**What:** Brief description of the issue or suggestion.
**Impact:** Why it matters.
```

### Conference/Talk Notes
```markdown

### [Conference/Event Name] - [Talk Title]
#conference #relevant-topic

**Speaker:** Name
**Format:** Keynote / Breakout / Workshop
**Date:** YYYY-MM-DD

**Key Takeaways:**
- Takeaway one
- Takeaway two

**Interesting Quotes/Stats:**
- Quote or stat

**Follow-up:**
- [ ] Action items from this talk
```

### Homelab Log

**For significant setups, builds, or fixes** (new node, new service, major config change), create a standalone note from template:
```bash
obsidian create vault="darko" path="AREAS/inbox/triglav-zfs-setup" template="homelab log"
```
Then fill in the `host:`, `ip:`, `date:` properties and append the details.

**For quick homelab updates** (small fix, observation, power reading), append to the daily note:
```markdown

### [Brief Description]
#homelab #relevant-tags

**Context:** What machine/service, what happened.
**Result:** What changed.
```

### Project Idea
```markdown

### Project Idea: [Name]
#idea #relevant-tech

**Problem:** What problem does this solve?
**Approach:** High-level approach
**Tech:** Technologies involved
**Effort:** Quick hack / Weekend project / Multi-week

**Notes:**
- Additional thoughts
```

### Person Note (new note, not daily)
Create using the vault template, then append details:
```bash
obsidian create vault="darko" path="RESOURCES/people/jane-doe" template="people"
```
Then fill in the fields by appending context:
```bash
obsidian append vault="darko" file="RESOURCES/people/jane-doe" content="
## Context
How Darko knows them, their role, organization.

## Conversation Notes
- Key information from this conversation"
```
Also set the inline properties the template creates:
```bash
obsidian property:set vault="darko" file="RESOURCES/people/jane-doe" name="name" value="Jane Doe"
obsidian property:set vault="darko" file="RESOURCES/people/jane-doe" name="email" value="jane@example.com"
```

### Pro Tip (dual-route)
Append to BOTH today's daily note AND `RESOURCES/protips.md`:

```markdown

### [Brief Title] (YYYY-MM-DD)
#protips #relevant-topic

The actual tip or insight. Be specific and actionable.
```

## Tag Selection Guide

Choose tags based on content. Common tags in Darko's vault:

| Domain | Tags |
|--------|------|
| AWS | `#aws`, `#lambda`, `#bedrock`, `#agentcore`, `#cdk` |
| Rust | `#rust`, `#cargo`, `#axum` |
| AI/Agents | `#agents`, `#strands`, `#mcp`, `#ai` |
| Content | `#video`, `#blog`, `#talk`, `#conference` |
| Homelab | `#homelab`, `#cluster`, `#optiplex`, `#zfs`, `#mqtt`, `#prometheus`, `#grafana`, `#iot`, `#tasmota` |
| Tools | `#kiro`, `#opencode`, `#neovim`, `#obsidian` |
| Work | `#meeting`, `#feedback`, `#product` |
| Meta | `#protips`, `#idea`, `#people` |
| Programs | `#programs`, `#devrel`, `#community` |

Always add at least one relevant tag. Multiple tags are encouraged when content spans domains.

## Critical Rules

1. **NEVER delete content.** Only append. Only exception: Darko explicitly asks to remove something.
2. **Read before writing.** Always check current state to avoid duplicates.
3. **Pro tips are dual-routed.** Always goes to both daily note AND `RESOURCES/protips.md`.
4. **Tags never in headers.** Put them on the line below.
5. **When unsure where to route, use the daily note.** It's the catch-all inbox.
6. **Confirm after capture.** Tell Darko what was saved and where.
7. **Format consistently.** Use the templates above as starting points, adapt as needed.
