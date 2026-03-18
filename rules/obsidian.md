# Obsidian Vault Awareness

Darko uses Obsidian for knowledge management. His vault is at `~/workspace/darko` (vault name: `darko`), organized with the PARA system (Projects, Areas, Resources, Archive). Daily notes live in `AREAS/inbox/YYYY-MM-DD.md`.

## Cardinal Rules

- **Never delete content** from notes unless explicitly told to. Only append.
- **Never put tags in markdown headers** - they don't render. Tags go on the line below.
- **Always add inline tags** when writing content (#rust, #aws, #agents, etc.)

## How to Interact with the Vault

All vault operations use the **Obsidian CLI** (the `obsidian` command). Load the appropriate skill for detailed instructions:

- **obsidian-daily** - Daily note reading, appending TODOs/wins/notes, pro tip routing
- **obsidian-vault** - Searching, reading notes, moving files, tag management, vault health
- **obsidian-capture** - Quick capture from conversations, routing to the right location

## Continuous Improvement

Skills, templates, and workflows are living documents. Proactively suggest improvements when you notice:

- **Template gaps** - Darko writes the same structure repeatedly without a template. Suggest creating one in `RESOURCES/templates/` and registering it in the `obsidian-vault` and `obsidian-capture` skills.
- **Template drift** - A template exists but Darko consistently adds fields or sections that aren't in it. Suggest updating the template to match how he actually uses it.
- **Routing gaps** - Content gets dumped into the daily note that would be better served by its own note or an existing note. Suggest updating the routing logic in `obsidian-capture`.
- **Tag inconsistency** - New tags emerge that aren't in the skill's tag guide. Suggest adding them.
- **Skill gaps** - An Obsidian workflow comes up that none of the three skills cover well. Suggest a skill update or a new skill.
- **CLI discoveries** - If a new Obsidian CLI command or pattern proves useful, suggest adding it to the relevant skill.

When suggesting improvements, be specific: name the file to change, describe the change, and explain why. Don't just say "we should update the template" - say "the product feedback template should add a `workaround:` field because you've included workarounds in 3 of your last 5 feedback entries."

Never make changes to skills or templates without asking first. Suggest, don't act.
