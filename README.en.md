# Dreamteam

> 🌎 Languages: [Português](README.md) • English • [Español](README.es.md)

**AI agent orchestration framework.**

Build agent teams with defined personalities, automated pipelines, and persistent memory, all inside your terminal, with no servers, no external APIs, and no complex setup.

---

## What is Dreamteam?

Dreamteam is a file-based framework that teaches AI agents to orchestrate multiple agents with distinct personas, run step-by-step pipelines, and accumulate memory across executions.

Compatible with **Claude Code** (Anthropic) and **Antigravity** (Google DeepMind). Just open the project in your preferred assistant and type `/dreamteam`.

You define **who** your agents are (personas), **what** they do (tasks), and **in what order** (pipeline). Dreamteam handles the rest: context loading, sequential execution, review checkpoints, output versioning, and learning memory.

### What you can build with Dreamteam

- A strategic council of VPs that debates your company's decisions
- A content team with a writer, reviewer, and strategist that produces posts, threads, and articles
- An analysis team that researches, synthesizes, and delivers structured reports
- Any process you would normally do wearing multiple "hats" at once

---

## Prerequisites

Choose one of the supported platforms:

- **[Claude Code](https://claude.ai/code)** (Anthropic) — via CLAUDE.md + `.claude/skills/` skill
- **[Antigravity](https://antigravity.dev)** (Google DeepMind) — via `.agent/rules/` + `.agent/workflows/`

No additional dependencies. No npm, pip, docker, or server configuration.

---

## Installation

### 1. Clone or download the repository

```bash
git clone https://github.com/JotaOdiceu/dreamteam.git
cd dreamteam
```

Or download the ZIP and extract it to a folder of your choice.

### 2. Open the project in Claude Code

Launch Claude Code and navigate to the project folder:

```bash
claude /path/to/dreamteam
```

Or open it via the desktop app by pointing it to the directory.

### 3. Run onboarding

On first launch, Dreamteam detects it is unconfigured and starts onboarding automatically:

```bash
/dreamteam
```

The assistant will ask for:

- Your name and preferences
- Output language (default: Portuguese, configurable to any language)
- Your project or company context

This information is saved to `_workspace/context.json` and loaded on every execution.

---

## Core Concepts

### Personas

A **persona** defines an agent: its identity, tone of voice, principles, and anti-patterns. This is what makes each agent distinct and consistent.

Dreamteam's key differentiator is that **personas are reusable across teams**. Define once in the global library (`personas/`) and use in as many teams as you want. Teams can also have local personas exclusive to them (`teams/{name}/personas/`).

```markdown
personas/
└── strategist.persona.md    ← available to any team

teams/my-team/
└── personas/
    └── specialist.persona.md  ← exclusive to this team
```

**Persona file format (`*.persona.md`):**

```markdown
---
id: strategist
name: Strategist
title: VP of Strategy and Positioning
icon: 📈
tags: [strategy, marketing, positioning]
version: 1.0.0
---

## Identity
Focused on "Where to Play and How to Win". Analytical and direct.

## Tone of Voice
Precise and results-oriented. Uses terms like "Competitive Advantage".
Never uses hype language.

## Principles
- Differentiate or die
- Strategy starts with the customer's problem, not the technology
- Strategy is deciding what NOT to do

## Specialties
Competitive analysis, market positioning, value proposition.

## Anti-patterns
Never approves "doing more of the same". Never accepts goals without metrics.
```

---

### Teams

A **team** is a group of agents with a defined pipeline. Lives in `teams/{name}/`.

```markdown
teams/my-team/
├── settings.json      ← single source: personas + pipeline
├── _memory/
│   ├── memories.json  ← team preferences and learnings
│   └── runs.json      ← execution history
├── output/            ← generated outputs (gitignored)
├── personas/          ← local personas (optional)
└── tasks/             ← pipeline task files
    ├── task-1.md
    └── task-2.md
```

**`settings.json` format:**

```json
{
  "icon": "🏛️",
  "name": "Strategic Council",
  "description": "Advisor board for analyzing executive decisions.",

  "personas": [
    {
      "id": "strategist",
      "name": "Strategist",
      "title": "VP of Strategy",
      "icon": "📈",
      "source": "global",
      "file": "personas/strategist.persona.md"
    },
    {
      "id": "moderator",
      "name": "Moderator",
      "title": "Council Secretary",
      "icon": "✍️",
      "source": "local",
      "file": "teams/council/personas/moderator.persona.md"
    }
  ],

  "pipeline": [
    {
      "id": "briefing",
      "name": "Challenge Collection",
      "persona": "moderator",
      "task": "tasks/briefing.md",
      "execution": "inline",
      "output": "output/briefing.md"
    },
    {
      "id": "review",
      "type": "checkpoint",
      "name": "Briefing Review",
      "message": "Review the briefing above. Does it look correct?"
    },
    {
      "id": "debate",
      "name": "Strategic Debate",
      "persona": "strategist",
      "task": "tasks/debate.md",
      "execution": "inline",
      "input": "output/briefing.md",
      "output": "output/debate.md"
    }
  ]
}
```

---

### Tasks

Each pipeline step points to a task file. Tasks define what the agent does, how it does it, and what it produces.

**Task file format (`tasks/*.md`):**

```markdown
---
id: briefing
name: "Challenge Collection"
persona: moderator
---

## Objective
Collect and structure the challenge or question brought by the user.

## Input Context
The user describes the situation or decision that needs to be analyzed.

## Process
1. Read the user's input
2. Identify the core of the decision: what's at stake?
3. Structure into: Context, Central Question, Constraints
4. Present for validation before advancing

## Output Format
Markdown document with sections: Context, Central Question, Constraints.

## Veto Conditions
- Output without a defined Central Question: reject
- Output over 500 words: reject and summarize
```

---

### Pipeline

The pipeline is the array of steps defined in `settings.json`. Each step can be:

| Type                      | What it does                                       |
|---------------------------|----------------------------------------------------|
| `"execution": "inline"`   | Agent executes and presents the result in the chat |
| `"execution": "subagent"` | Agent works in the background                      |
| `"type": "checkpoint"`    | Pauses execution and waits for user approval       |

The Runner automatically validates:

- That a step's input exists before executing
- That output was generated after execution
- That veto conditions were not violated (and requests correction)

---

### Memory

Each team accumulates memory across executions:

- **`memories.json`**: preferences and patterns extracted from explicit user feedback (what they approved, rejected, or requested directly)
- **`runs.json`**: chronological log of all executions with topic, output, and result

Memory is loaded at the start of each execution and influences agent behavior.

---

## Commands

### Interactive menu

```bash
/dreamteam
```

Opens the main menu with options to create, run, edit teams, and manage personas.

---

### Team commands

| Command                    | What it does                             |
|----------------------------|------------------------------------------|
| `/dreamteam create`        | Launches the wizard to create a new team |
| `/dreamteam teams`         | Lists all teams in `teams/`              |
| `/dreamteam run <name>`    | Runs a team's pipeline                   |
| `/dreamteam edit <name>`   | Edits an existing team                   |
| `/dreamteam delete <name>` | Deletes a team (with confirmation)       |

### Persona commands

| Command                             | What it does                 |
|-------------------------------------|------------------------------|
| `/dreamteam personas`               | Opens the persona library    |
| `/dreamteam personas new`           | Creates a new global persona |
| `/dreamteam personas edit <name>`   | Edits an existing persona    |
| `/dreamteam personas delete <name>` | Removes a persona            |

### Workspace and settings

| Command              | What it does                            |
|----------------------|-----------------------------------------|
| `/dreamteam context` | View or edit the workspace context      |
| `/dreamteam help`    | Displays the full help text             |
| `/dreamteam reset`   | Resets all settings (with confirmation) |

---

## Directory Structure

```markdown
dreamteam/
│
├── CLAUDE.md                    ← Project instructions for Claude Code
│
├── .claude/
│   └── skills/
│       └── dreamteam/
│           └── SKILL.md         ← Main skill (entry point for /dreamteam)
│
├── _core/                       ← Framework core
│   ├── architect.md             ← Architect instructions (creates/edits teams)
│   └── runner.md                ← Pipeline Runner instructions (executes teams)
│
├── _workspace/
│   ├── context.example.json ← context template (copy and rename)
│   └── context.json         ← your project/business context (gitignored)
│
├── personas/                    ← Global reusable persona library
│   └── strategist.persona.md    ← Included example
│
└── teams/                       ← Your teams live here
    └── my-team/
        ├── settings.json
        ├── _memory/
        ├── output/
        ├── personas/
        └── tasks/
```

> **Do not manually modify** files in `_core/` unless you know what you are doing. `CLAUDE.md`, `_workspace/`, and `personas/` files can be freely edited.

---

## Step-by-step example

### 1. Create a strategic analysis team

```bash
/dreamteam create "A council with a moderator and strategist to analyze business decisions"
```

The Architect will:

1. Propose the team structure and pipeline
2. Check if compatible personas exist in the global library
3. Create or reuse personas
4. Build all necessary files

### 2. Run the team

```bash
/dreamteam run strategic-council
```

The Runner will:

1. Load all personas
2. Execute step 1: collect the challenge
3. Checkpoint: you review the briefing
4. Execute step 2: strategic analysis
5. Save output to `teams/strategic-council/output/{run_id}/v1/`
6. Update the team's memory

### 3. Add a new persona to the library

```bash
/dreamteam personas new
```

The assistant collects the persona data and creates `personas/{id}.persona.md` ready to use in any team.

---

## Output Versioning

Each execution creates a unique timestamped folder:

```markdown
teams/my-team/output/
└── 2026-04-16-143022/
    └── v1/
        ├── briefing.md
        └── analysis.md
```

If the same step runs more than once in the same run (due to rejection or retry), versioning automatically advances to `v2/`, `v3/`, etc.

---

## Honorable Mention

Dreamteam is a **lightweight reimplementation** of [**Opensquad**](https://github.com/renatoasse/opensquad), a full-featured multi-agent orchestration framework with support for pluggable skills, social media profile investigation (Sherlock), a publishing engine, and much more.

If Dreamteam covers what you need, great. If you need something more robust, with a skills catalog, Playwright integration, and a broader ecosystem, Opensquad is the right path.

Dreamteam exists because you do not always need everything. Sometimes you just need well-defined personas, clear pipelines, and memory that works.

---

## License

MIT. Use, modify, and distribute freely.

---

## Powered by

jotaodiceu.dev
