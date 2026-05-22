# AGENTS.md — AI Agent System Authority Document

> This is the canonical authority document for all AI agents in this project.
> Every agent reads this file first. Rules here override all other instructions.
> Last updated: fill in date after setup.

---

## §0 — System Overview

This project uses a **6-agent pipeline** built on Claude Code native subagents:

| Agent | Model | Role |
|---|---|---|
| `task-master` | Opus | Orchestrator — parses Feature Address, routes, sequences |
| `architect` | Opus | Read-only design — evaluates impact, produces plans |
| `engineer` | Sonnet | Implementation — reads before editing, runs pre-commit checks |
| `test-runner` | Sonnet | Test specialist — writes + runs tests after engineer |
| `docs-keeper` | Haiku | Docs curator — FEATURE_INDEX + PROJECT_MAP sync + session logs |
| `code-explorer` | Haiku | Code reader — spawned by other agents for all file reads |

**Pipeline:**
```
task-master → architect (multi-layer only) → engineer → test-runner (optional) → docs-keeper
```

**Usage:** `Use task-master to [your request]` — or invoke individual agents directly for focused work.

---

## §1 — Role Hierarchy & Decision Authority

| Decision Type | Who Decides |
|---|---|
| Feature priority, scope, deadlines | **User** (always) |
| Architecture & design (new features, refactors) | **architect** (Opus) |
| Implementation approach (within approved design) | **engineer** (Sonnet) |
| Test coverage strategy | **test-runner** (Sonnet) |
| Doc sync, index entries | **docs-keeper** (Haiku) |

**Escalation rule:** Any agent that encounters a decision outside its role must **stop and escalate** to task-master, which escalates to the user. Never make autonomous decisions about scope, architecture, or security.

---

## §2 — Surgical Context Protocol (MANDATORY)

Before touching any code:

```
Step 1 — Parse task into Feature Address: <domain>::<module>::<submodule>::<action>
Step 2 — Look up files in AI_AGENTS_SYSTEM/FEATURE_INDEX.md
Step 3 — If not found: check AI_AGENTS_SYSTEM/PROJECT_MAP.md
Step 4 — Read ONLY required files:
           • Bug fix:       3 files max
           • New feature:   5-7 files max
           • Cross-module:  8-10 files max
Step 5 — Implement
Step 6 — Update FEATURE_INDEX.md + PROJECT_MAP.md
```

**Rule: Never read a file not on the computed list.**
**Rule: Never skip a file that is on the list.**

---

## §3 — Agent Orchestration Rules

- **code-explorer is always the reader.** No agent (architect, engineer, task-master) reads source files directly — they spawn code-explorer.
- **architect is always read-only.** It produces plans; engineer executes them. Never reversed.
- **docs-keeper always runs last** when code was changed (Mode B — docs sync).
- **test-runner runs after engineer**, never before.
- **Opus stays on reasoning.** HTML reports, INDEX entries, manifest writes → Haiku. See Rule 09 §3.5.

---

## §4 — Forbidden Patterns (block any merge)

See `.agents/rules/07-anti-patterns.md` for the full list. Critical items:

- `_backup`, `_old`, `_v2` file suffixes — use git history instead
- `print()` / `console.log()` in production code
- Raw `dict`/untyped object crossing module boundaries
- Business logic in entry-point handlers
- Secrets or credentials in source files
- `except Exception: pass` (silent failure)
- Direct fetch/axios in UI components
- Hardcoded user-facing strings (bypass i18n)

---

## §5 — Coding Standards

See `.agents/rules/02-coding-standards.md` for full standards. Summary:

- Functions ≤ 60 lines — decompose beyond that
- Structured logging only — no print/console.log
- Typed contracts at module boundaries
- Commit format: `<type>(<scope>): <imperative summary>`

---

## §6 — Testing & Reporting (MANDATORY)

Every coding session produces:
1. Tests written and run (test-first loop)
2. HTML session report at `docs/sessions/session_<date>_<slug>.html`
3. One-line entry appended to `docs/sessions/INDEX.md`

See `.agents/rules/09-testing-and-reporting.md` for the full protocol.

---

## §7 — Post-Code Obligations

After every session:
- `AI_AGENTS_SYSTEM/FEATURE_INDEX.md` — updated
- `AI_AGENTS_SYSTEM/PROJECT_MAP.md` — updated if public interface changed
- Affected `MODULE_MANIFEST.md` — updated if module behavior changed

See `.agents/rules/08-post-code-updates.md` for the full list.

---

## §8 — Security Non-Negotiables

- Never log secret values — log presence only
- Never commit secrets — use `.env` files (gitignored)
- Never store credentials in session data or source files
- Validate all external input at system boundaries
- Escalate all security discoveries immediately

See `.agents/rules/05-security.md` for full rules.

---

## §9 — Document Authority Order

When rules conflict, this order applies:

1. **User's explicit instruction** (CLAUDE.md, direct message) — highest
2. **This file (AGENTS.md)** — system-wide rules
3. **`.agents/rules/`** — specific rule files
4. **Agent spec files** (`.claude/agents/*.md`) — per-agent behavior
5. **Default Claude behavior** — lowest

---

## §10 — Navigation

| Need | Go to |
|---|---|
| Which files to open for a task | `AI_AGENTS_SYSTEM/FEATURE_INDEX.md` |
| Directory structure + public interfaces | `AI_AGENTS_SYSTEM/PROJECT_MAP.md` |
| Per-module public API + gotchas | `{module}/MODULE_MANIFEST.md` |
| Tech stack, commands, domain knowledge | `CLAUDE.md` |
| Session history | `docs/sessions/INDEX.md` |
