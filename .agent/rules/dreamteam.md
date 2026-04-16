---
name: dreamteam
---

# Dreamteam — Project Instructions

This project uses **Dreamteam**, a multi-agent orchestration framework built for AI coding assistants.

## Quick Start

Type `/dreamteam` to open the main menu, or use any of these commands:
- `/dreamteam create` — Create a new team
- `/dreamteam run <name>` — Run a team's pipeline
- `/dreamteam teams` — List all teams
- `/dreamteam personas` — Manage the persona library
- `/dreamteam help` — See all commands

## Directory Structure

```
_core/          — Framework core (do not edit manually)
_workspace/     — Persistent workspace context
personas/       — Global reusable persona library
teams/          — User-created teams
teams/{name}/
  settings.json     — Team definition and pipeline
  _memory/          — Persistent team memory
  output/           — Generated outputs
  personas/         — Local personas (exclusive to this team)
  tasks/            — Pipeline task files
```

## How It Works

1. The `/dreamteam` workflow is the entry point for all interactions
2. The **Architect** creates and modifies teams (reads `_core/architect.md`)
3. Personas live in `personas/` (global) or `teams/{name}/personas/` (local)
4. The **Pipeline Runner** executes teams step by step (reads `_core/runner.md`)
5. All tasks run inline and sequentially — no background subagents
6. Checkpoints pause execution for user input/approval

## Personas: the Dreamteam difference

Personas are **reusable across teams**. Create a persona once in `personas/` and use it in any team. Teams can also have local exclusive personas in `teams/{name}/personas/`.

**Persona format (`personas/{id}.persona.md`):**
```markdown
---
id: persona-id
name: Full Name
title: Role or Position
icon: 🎯
tags: [tag1, tag2]
version: 1.0.0
---

## Identity
## Tone of Voice
## Principles
## Specialties
## Anti-patterns
```

## Team format (`settings.json`)

```json
{
  "icon": "🚀",
  "name": "Team Name",
  "description": "One-line description",
  "version": "1.0.0",
  "personas": [
    { "id": "...", "name": "...", "title": "...", "icon": "...", "source": "global", "file": "personas/....persona.md" }
  ],
  "pipeline": [
    { "id": "step-1", "name": "...", "persona": "...", "task": "tasks/....md", "execution": "inline", "output": "output/....md" },
    { "id": "checkpoint-1", "type": "checkpoint", "name": "...", "message": "..." }
  ]
}
```

## Rules

- Always use `/dreamteam` commands to interact with the system
- Do not edit files in `_core/` manually
- `settings.json` for each team can be edited manually if needed
- Context in `_workspace/context.md` is loaded in all runs

## Interaction Rules

- NEVER ask more than one question per message — always wait for the user's answer before proceeding
- When presenting options, always use a numbered list (1. / 2. / 3.) — tell the user to reply with the option number
- All tasks run inline and sequentially: complete each step before moving to the next
- Never announce you "will do something" and then skip the work — do it before continuing
