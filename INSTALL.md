# INSTALL.md — Setup Guide for Claude

> **This file is written for Claude to read and execute.**
> When a user says "setup AI agent system" or "apply ai-agent-system from [path]",
> Claude reads this file and follows the steps below to install the system into their project.
>
> Human reading this: see README.md for the quick-start guide.

---

## What You Will Install

A 6-agent AI development pipeline for Claude Code:

```
.claude/agents/          ← 6 subagent specs (task-master, architect, engineer,
                            test-runner, docs-keeper, code-explorer)
.agents/rules/           ← 7 development rules (coding standards, testing, security...)
AI_AGENTS_SYSTEM/        ← Authority docs (AGENTS.md, FEATURE_INDEX.md, PROJECT_MAP.md...)
docs/sessions/           ← HTML session report template + index
.claude/settings.json    ← Hooks (PostToolUse docs reminder + Stop checklist)
CLAUDE.md                ← Project instructions (customized for this project)
```

---

## Step 1 — Ask the User These Questions (one at a time)

Before copying any files, gather this information:

1. **Project name?**
   - Use in headings, agent descriptions, CLAUDE.md title

2. **Primary language + framework?**
   - Examples: `Python + FastAPI`, `Node.js + Express`, `Go + Chi`, `Java + Spring Boot`, `Ruby + Rails`
   - This determines which coding standards and test commands to include

3. **Test command?**
   - Examples: `pytest`, `npm test`, `go test ./...`, `bundle exec rspec`

4. **Does this project have a frontend?**
   - Yes → ask: which framework? (React, Vue, Angular, Svelte, none)
   - No → skip frontend sections in CLAUDE.md

5. **Brief project description (1-2 sentences)?**
   - Used in the Project Overview section of CLAUDE.md

Store these answers — you will use them in Steps 3 and 4.

---

## Step 2 — Scan the Project

Before copying files, understand the target project structure:

```
1. List top-level directories and files
2. Look for: package.json / pyproject.toml / go.mod / pom.xml / Gemfile
   → confirms primary language if user was unsure
3. Identify the main source directory (src/, app/, backend/, lib/, etc.)
4. Identify the test directory (tests/, __tests__/, spec/, test/, etc.)
5. Identify the frontend directory if present (frontend/, client/, web/, etc.)
6. Check if .claude/ already exists
   → If yes: warn user, ask before overwriting settings.json
7. Check if CLAUDE.md already exists
   → If yes: read it first; merge rather than overwrite
```

Note what you found — you will use this in Step 4 (PROJECT_MAP.md).

---

## Step 3 — Copy Files

Copy from the ai-agent-system repo to the target project.
Create directories as needed.

| Source (in ai-agent-system/) | Destination (in target project) | Action |
|---|---|---|
| `agents/task-master.md` | `.claude/agents/task-master.md` | copy |
| `agents/architect.md` | `.claude/agents/architect.md` | copy |
| `agents/engineer.md` | `.claude/agents/engineer.md` | copy |
| `agents/test-runner.md` | `.claude/agents/test-runner.md` | copy |
| `agents/docs-keeper.md` | `.claude/agents/docs-keeper.md` | copy |
| `agents/code-explorer.md` | `.claude/agents/code-explorer.md` | copy |
| `rules/01-surgical-context.md` | `.agents/rules/01-surgical-context.md` | copy |
| `rules/02-coding-standards.md` | `.agents/rules/02-coding-standards.md` | copy |
| `rules/03-architecture.md` | `.agents/rules/03-architecture.md` | copy |
| `rules/05-security.md` | `.agents/rules/05-security.md` | copy |
| `rules/07-anti-patterns.md` | `.agents/rules/07-anti-patterns.md` | copy |
| `rules/08-post-code-updates.md` | `.agents/rules/08-post-code-updates.md` | copy |
| `rules/09-testing-and-reporting.md` | `.agents/rules/09-testing-and-reporting.md` | copy |
| `system-docs/AGENTS.md` | `AI_AGENTS_SYSTEM/AGENTS.md` | copy |
| `system-docs/FEATURE_INDEX.md` | `AI_AGENTS_SYSTEM/FEATURE_INDEX.md` | copy |
| `system-docs/PROJECT_MAP.md` | `AI_AGENTS_SYSTEM/PROJECT_MAP.md` | copy |
| `system-docs/MODULE_MANIFEST_TEMPLATE.md` | `AI_AGENTS_SYSTEM/MODULE_MANIFEST_TEMPLATE.md` | copy |
| `system-docs/AI_ARCHITECTURE.md` | `AI_AGENTS_SYSTEM/AI_ARCHITECTURE.md` | copy |
| `reports/session-template.html` | `docs/sessions/_template.html` | copy |
| `settings/claude-settings.json` | `.claude/settings.json` | copy (Windows/PowerShell) |
| `settings/claude-settings-bash.json` | `.claude/settings.json` | copy (Mac/Linux/bash) |
| `templates/CLAUDE.md` | `CLAUDE.md` | customize (see Step 4) |

**OS detection:** Check if the user is on Windows (PowerShell) or Mac/Linux (bash).
- Windows → use `settings/claude-settings.json`
- Mac/Linux → use `settings/claude-settings-bash.json`

---

## Step 4 — Customize CLAUDE.md

After copying `templates/CLAUDE.md` to the project root, fill in every `[FILL: ...]` placeholder:

### 4.1 Header
- Replace `[PROJECT_NAME]` with the project name from Step 1

### 4.2 Surgical Context Protocol — Domains
Replace the example domains with domains that match the project's actual architecture:

```
# For a Python FastAPI + React project:
  api      → backend/api/ route handlers
  service  → backend/services/ business logic
  core     → backend/core/ shared utilities
  ui       → frontend/src/ components, hooks, stores

# For a Go monolith:
  cmd      → cmd/ CLI entry points
  handler  → internal/handler/ HTTP handlers
  service  → internal/service/ business logic
  repo     → internal/repo/ data access
  pkg      → pkg/ shared utilities

# For a Node.js Express API:
  routes   → src/routes/ Express routers
  service  → src/services/ business logic
  model    → src/models/ data access
  util     → src/utils/ shared helpers
```

### 4.3 Project Overview
Fill in 2-3 sentences describing what the project does, its architecture, and key characteristics.

### 4.4 Development Commands
Fill in the actual commands to:
- Install dependencies
- Run the backend
- Run the frontend (if applicable)
- Run tests
- Run linters

### 4.5 Architecture Section
Describe the actual layer structure based on what you found in Step 2.
List the real directory names and their purposes.

### 4.6 Domain-Critical Knowledge
Ask the user: "Is there anything domain-specific I should know about this project that would help avoid mistakes? For example: external API quirks, special data formats, business rules, known gotchas."

Add their answer to the Domain-Critical Knowledge section.

---

## Step 5 — Customize Agent Specs

In each of the 6 copied agent files (`.claude/agents/*.md`), update:

### 5.1 Description line (YAML frontmatter)
Replace `[PROJECT_NAME]` with the actual project name.

```yaml
# Before:
description: Primary orchestrator for [PROJECT_NAME].

# After:
description: Primary orchestrator for MyApp — a Node.js + React SaaS platform.
```

### 5.2 Tech Stack table in engineer.md
Fill in the actual tech stack from Step 1 answers:

```markdown
| Primary language | Python 3.11+ |
| Backend framework | FastAPI + uvicorn |
| Database | PostgreSQL via SQLAlchemy |
| Frontend framework | React 18 + TypeScript |
| Testing | pytest + pytest-asyncio |
| Linting | black + flake8 + mypy |
```

### 5.3 Project Quick Reference in code-explorer.md
Replace the placeholder directory tree with the actual structure found in Step 2.

### 5.4 Test commands in test-runner.md
Replace the `[test command]` placeholders with the actual test commands.

---

## Step 6 — Initialize Document System

### 6.1 FEATURE_INDEX.md
The copied file is empty (just the header). Do NOT pre-populate it.
It will be filled in as features are worked on.

### 6.2 PROJECT_MAP.md
Update the Directory Tree section with the actual project structure from Step 2.
Replace the placeholder tree with the real directories and their purposes.

### 6.3 Create docs/sessions/INDEX.md
Create this file with the following content:

```markdown
# Session Reports — [PROJECT_NAME]

> Chronological log of all AI coding sessions.
> Each entry links to the HTML report for that session.

| Date | Slug | Summary |
|---|---|---|
```

### 6.4 Create .env.example (if it doesn't exist)
If the project has no `.env.example`, create one with a placeholder:

```bash
# Copy this file to .env and fill in the values
# PROJECT_NAME=[PROJECT_NAME]

# Add your environment variables here
```

---

## Step 7 — Validate

Before declaring setup complete, verify:

- [ ] `.claude/agents/` contains 6 `.md` files
- [ ] `.agents/rules/` contains 7 `.md` files
- [ ] `AI_AGENTS_SYSTEM/` contains 5 files (AGENTS.md, FEATURE_INDEX.md, PROJECT_MAP.md, MODULE_MANIFEST_TEMPLATE.md, AI_ARCHITECTURE.md)
- [ ] `docs/sessions/_template.html` exists
- [ ] `docs/sessions/INDEX.md` exists
- [ ] `.claude/settings.json` exists with hooks configured
- [ ] `CLAUDE.md` exists with no remaining `[FILL: ...]` placeholders
- [ ] Agent specs have no remaining `[PROJECT_NAME]` placeholders
- [ ] `engineer.md` tech stack table is filled in
- [ ] `code-explorer.md` project quick reference reflects actual structure

---

## Step 8 — Confirm to User

When setup is complete, show the user a summary:

```
## AI Agent System — Setup Complete

**Project:** [PROJECT_NAME]
**Files installed:** [N] files across 6 directories

**How to use:**
  "Use task-master to [your request]"    → full pipeline
  "Use engineer to [your request]"       → implement directly
  "Use architect to [your request]"      → design only

**Key files:**
  CLAUDE.md                              → project instructions
  AI_AGENTS_SYSTEM/FEATURE_INDEX.md      → task → file navigation
  docs/sessions/                         → HTML session reports (auto-generated)

**Next step:** Start a coding session and say "Use task-master to [first task]"
```

---

## Troubleshooting

**"I already have a CLAUDE.md"**
Read the existing file first. Merge the AI agent system sections in — don't overwrite project-specific content that's already there.

**"I already have .claude/settings.json"**
Merge the hooks from `settings/claude-settings.json` into the existing file. Don't overwrite other settings.

**"The project uses a language not covered in engineer.md"**
Fill in the tech stack table with the actual language. The agent patterns (code-reading, delegation, pre-commit checks) apply universally — only the specific commands change.

**"I'm not sure what domains to use"**
Use the top-level source directories as domains. If the project has `api/`, `services/`, `utils/` — those become your domains. Keep it simple: 3-5 domains maximum.
