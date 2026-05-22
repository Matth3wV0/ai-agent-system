# AI Agent System

A portable 6-agent development pipeline for Claude Code. Brings structured multi-agent orchestration, mandatory test-first workflows, and rich HTML session reports to any project.

---

## What This Is

A set of Claude Code native subagents and development rules that work together as a team:

| Agent | Model | Role |
|---|---|---|
| `task-master` | Opus | Orchestrator — parses requests, routes to the right agents in the right order |
| `architect` | Opus | Read-only design — evaluates impact, flags forbidden patterns, produces plans |
| `engineer` | Sonnet | Implementation — reads before editing, runs pre-commit checks |
| `test-runner` | Sonnet | Test specialist — writes + runs tests, never uses real external APIs |
| `docs-keeper` | Haiku | Docs curator — keeps FEATURE_INDEX and PROJECT_MAP in sync |
| `code-explorer` | Haiku | Code reader — all file reads delegated here to keep other agents' context clean |

**Pipeline:**
```
task-master → architect (multi-layer only) → engineer → test-runner → docs-keeper
```

**Usage in any project:**
```
"Use task-master to add user profile endpoint"
"Use engineer to fix the login error"
"Use architect to design the notification system"
```

---

## Quick Start

### 1. Clone this repo

```bash
git clone https://github.com/Matth3wV0/ai-agent-system ~/.ai-agent-system
```

### 2. Open Claude Code in your target project

```bash
cd /path/to/your-project
claude
```

### 3. Tell Claude to set up the system

```
Setup AI agent system from ~/.ai-agent-system
```

Claude reads `INSTALL.md`, asks you 5 questions about your project, then copies and customizes all files automatically. Takes about 5 minutes.

### 4. Start using it

```
Use task-master to [your first task]
```

---

## What Gets Installed

```
your-project/
├── .claude/
│   ├── agents/                    ← 6 subagent specs
│   │   ├── task-master.md
│   │   ├── architect.md
│   │   ├── engineer.md
│   │   ├── test-runner.md
│   │   ├── docs-keeper.md
│   │   └── code-explorer.md
│   └── settings.json              ← Hooks (docs reminder + post-code checklist)
│
├── .agents/
│   └── rules/                     ← 7 development rules
│       ├── 01-surgical-context.md
│       ├── 02-coding-standards.md
│       ├── 03-architecture.md
│       ├── 05-security.md
│       ├── 07-anti-patterns.md
│       ├── 08-post-code-updates.md
│       └── 09-testing-and-reporting.md
│
├── AI_AGENTS_SYSTEM/
│   ├── AGENTS.md                  ← Authority document (all agent rules)
│   ├── FEATURE_INDEX.md           ← Task → file navigation index
│   ├── PROJECT_MAP.md             ← Directory topology + public interfaces
│   ├── MODULE_MANIFEST_TEMPLATE.md
│   └── AI_ARCHITECTURE.md        ← AI/ML component inventory template
│
├── docs/sessions/
│   ├── _template.html             ← HTML session report template
│   └── INDEX.md                   ← Chronological session log
│
└── CLAUDE.md                      ← Project instructions (customized for your project)
```

---

## Key Features

### Surgical Context Protocol
Every task maps to a `domain::module::submodule::action` Feature Address. FEATURE_INDEX.md returns exactly which files to open — no more, no less. Agents never read files they don't need.

### Cost-Aware Model Routing
- **Opus** (expensive): orchestration, design decisions, root-cause analysis
- **Sonnet** (mid): implementation, test writing
- **Haiku** (cheap): file reads, doc updates, HTML report generation

HTML session reports and documentation updates are explicitly forbidden on Opus. A PostToolUse hook fires after every file edit to remind Claude to sync the docs.

### Mandatory Test-First + HTML Reports
Every coding session produces:
1. Tests written and run before declaring done
2. An HTML session report with tabs, status bar, copy buttons, and collapsible diffs
3. An entry in `docs/sessions/INDEX.md`

See `.agents/rules/09-testing-and-reporting.md` for the full protocol.

### Self-Enforcing Hooks
`.claude/settings.json` wires two hooks:
- **PostToolUse** (after every Write/Edit): reminds Claude to update FEATURE_INDEX.md
- **Stop** (end of session): shows post-code checklist before closing

---

## Repo Contents

```
ai-agent-system/
├── README.md                       ← This file
├── INSTALL.md                      ← Claude-readable setup instructions
│
├── agents/                         ← Generic agent specs
├── rules/                          ← Generic development rules
├── system-docs/                    ← Generic AI_AGENTS_SYSTEM/ templates
├── settings/                       ← .claude/settings.json (bash + PowerShell)
├── reports/                        ← session-template.html
└── templates/
    └── CLAUDE.md                   ← CLAUDE.md template with [FILL] placeholders
```

---

## Customization After Setup

CLAUDE.md has a **Domain-Critical Knowledge** section for project-specific rules that don't belong in generic rule files — external API quirks, business rules, data format gotchas, known constraints.

Agent specs have a **Tech Stack** table (in engineer.md) and a **Project Quick Reference** (in code-explorer.md) that are filled in during setup and should be updated as the project evolves.

---

## Origin

This system was extracted from a production full-stack scraping project (Facebook Scrape V7) where it ran through multiple feature waves. The core patterns — Surgical Context Protocol, cost-aware model routing, test-first HTML reports, and the 6-agent pipeline — proved effective enough to package for reuse.

The HTML report design is based on Thariq Shihipar's post *"The Unreasonable Effectiveness of HTML"* from the Anthropic Claude Code team.

---

## License

MIT
