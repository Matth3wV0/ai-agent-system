# CLAUDE.md — [PROJECT_NAME]

> Project instructions for Claude Code. Every agent reads this file first.
> Filled in during setup by Claude following INSTALL.md.

---

## REQUIRED READING — LOAD BEFORE ANY TASK

- `AI_AGENTS_SYSTEM/FEATURE_INDEX.md` — task → file navigation index
- `AI_AGENTS_SYSTEM/AGENTS.md` — agent rules and authority document
- `.agents/rules/09-testing-and-reporting.md` — MANDATORY test-first loop + HTML session report

---

## SURGICAL CONTEXT PROTOCOL — MANDATORY BEFORE ANY CODE

Before touching any code, follow this algorithm:

```
Step 1 — Parse task into Feature Address:
  <domain>::<module>::<submodule>::<action>

  Domains for [PROJECT_NAME]:
  [FILL: list domains and what they cover, e.g.]
    api       → backend route handlers
    service   → business logic
    core      → shared utilities, storage, config
    ui        → frontend components, hooks, stores

Step 2 — Look up files in AI_AGENTS_SYSTEM/FEATURE_INDEX.md
Step 3 — If not found: check AI_AGENTS_SYSTEM/PROJECT_MAP.md
Step 4 — Read ONLY required files (3 for bug fix, 5-7 for feature, 8-10 for cross-module)
Step 5 — Implement
Step 6 — Update FEATURE_INDEX.md + PROJECT_MAP.md
```

---

## AI Agent System

Authority document: `AI_AGENTS_SYSTEM/AGENTS.md`

**Pipeline:**
```
task-master (Opus) → architect (Opus, multi-layer only) → engineer (Sonnet)
  → test-runner (Sonnet, optional) → docs-keeper (Haiku)
```

**Usage:** `Use task-master to [your request]`

---

## Project Overview

[FILL: 2-3 sentences describing what this project does, its primary purpose, and key technical characteristics]

Example: Full-stack web application for managing X. React/TypeScript frontend, FastAPI/Python backend, PostgreSQL database. Multi-tenant with JWT authentication and real-time WebSocket updates.

---

## Development Commands

### Backend
```bash
[FILL: commands to setup and run backend]
# e.g.:
cd backend
python -m venv venv
venv/bin/activate          # Mac/Linux
venv\Scripts\activate      # Windows
pip install -r requirements.txt
python main.py             # Runs on http://localhost:8000
```

### Frontend (if applicable)
```bash
[FILL: commands to setup and run frontend]
# e.g.:
cd frontend
npm install
npm run dev                # Runs on http://localhost:3000
npm run build
npm run lint
```

### Tests
```bash
[FILL: test commands]
# e.g.:
cd backend && pytest
cd frontend && npx tsc --noEmit && npm test
```

### Code Quality
```bash
[FILL: linting/formatting commands]
# e.g.:
cd backend && black . && flake8 . && mypy .
```

---

## Architecture

[FILL: describe your actual layer structure]

**Backend** (`[backend_dir]/`):

```
[Entry Layer] ([path]/)    → Validates input, calls services. NEVER contains business logic.
[Service Layer] ([path]/)  → Business logic and orchestration.
[Data Layer] ([path]/)     → Data access, external integrations.
[Core Layer] ([path]/)     → Shared utilities, config, models. NEVER platform-specific logic.
```

**Frontend** (`[frontend_dir]/src/`) (if applicable):
- `pages/` — Route-level components
- `components/` — Reusable UI components
- `services/` — API call abstractions (all HTTP calls go here)
- `types/` — All TypeScript types in single file

**Communication**: REST `/api/*` [+ WebSocket `/ws/*` if applicable]

---

## CODING STANDARDS — NON-NEGOTIABLE

### Forbidden Patterns
- No filename suffixes: `_fixed`, `_v2`, `_enhanced`, `_new`, `_old`, `_backup`
- No mock implementations in the production path
- No inline comments unless the WHY is non-obvious
- No hardcoded secrets, tokens, API keys, or credentials in source code
- No `print()` / `console.log()` — use structured logger exclusively
- No `except Exception: pass` or swallowed errors
- No function > 60 lines — decompose
- No circular imports — module graph must be a DAG
- No duplicate logic across modules — extract to shared on second occurrence
- No raw `dict` crossing module boundaries — use typed models

### Naming Conventions
[FILL: or keep these defaults]

**Python:**
- Types/classes: `PascalCase`
- Functions/variables: `snake_case`
- Constants: `SCREAMING_SNAKE_CASE`
- Private: `_snake_case` prefix
- Type hints on all function signatures

**TypeScript (if applicable):**
- Components: `PascalCase`
- Hooks: `camelCase` with `use` prefix
- Services: `camelCase` with `Api` suffix
- All types in `types/index.ts`
- No `any`

### Logging
```python
# Correct
logger.info("operation_complete", extra={"entity_id": entity.id, "count": n})
# Wrong
print(f"Done: {entity.id}")
```

---

## ANTI-PATTERNS — PROHIBITED

[FILL: add project-specific anti-patterns here. Generic ones are in .agents/rules/07-anti-patterns.md]

- Business logic in route/handler functions
- Direct `fetch()`/`axios` in UI components (use services layer)
- Hardcoded user-facing strings (use i18n)
- Storing credentials anywhere except environment variables
- Polling for progress when WebSocket/SSE is available

---

## SECURITY RULES

- NEVER log secret values — log presence only (`api_key=PRESENT`)
- NEVER commit secrets — use `.env` files (gitignored)
- NEVER store credentials in session data or source files
- Sanitize all external input at system boundaries
- Restrict CORS to known origins — never `*` in production
- No `eval`, `exec`, or dynamic code execution

---

## DOMAIN-CRITICAL KNOWLEDGE

[FILL: anything an AI agent must know to avoid making mistakes specific to your domain]

Examples of what to document here:
- External API quirks (rate limits, authentication, response format gotchas)
- Data format rules (e.g. date formats, currency handling, locale-specific parsing)
- Business rules that are non-obvious from the code
- Known production constraints (memory limits, timeout budgets, concurrency limits)
- Third-party library gotchas

---

## POST-CODE OBLIGATIONS (mandatory after every session)

### MANDATORY
- `AI_AGENTS_SYSTEM/FEATURE_INDEX.md` — add/update entry for every touched feature
- `AI_AGENTS_SYSTEM/PROJECT_MAP.md` — update if any public signature changed

### CONDITIONAL
- `.env.example` — if new env vars added
- `types/index.ts` — if new TypeScript types added (if applicable)
- i18n locale files — if new user-facing strings added (if applicable)

### Commit Message Format
```
<type>(<scope>): <imperative summary>

Types: feat | fix | refactor | perf | test | docs | chore | security
Scope: module name
```

### Escalate to User When
- Any change to authentication or authorization logic
- Introducing a new external dependency
- Architectural change affecting 3+ modules
- Security-relevant issue discovered
- Breaking change to a public API

---

## Prerequisites

[FILL: what needs to be installed before the project runs]
- [e.g. Python 3.11+, Node.js 20+]
- [e.g. PostgreSQL 15+]
- Copy `.env.example` → `.env` and fill in values
