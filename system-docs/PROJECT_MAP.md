# PROJECT_MAP — [PROJECT_NAME]

> Topology file. Updated on every PR that adds/removes/renames a module or public function.
> This file answers: "What exists and how it connects."

---

## Directory Tree

> Replace this with your actual project structure after setup.
> Claude will scan your project during install and populate this section.

```
[PROJECT_NAME]/
│
├── [backend_dir]/                  # Backend source
│   ├── [entry_layer]/              # API handlers / CLI / event handlers
│   ├── [service_layer]/            # Business logic / domain services
│   ├── [data_layer]/               # Repositories / data access
│   └── [core_layer]/               # Shared utilities, config, models
│
├── [frontend_dir]/                 # Frontend source (if applicable)
│   ├── pages/ or routes/           # Route-level components
│   ├── components/                 # Reusable UI components
│   ├── services/                   # API call abstractions
│   └── types/                      # Shared TypeScript types
│
├── tests/                          # Test files
├── docs/
│   └── sessions/                   # HTML session reports (Rule 09)
│       ├── _template.html          # Report template
│       └── INDEX.md                # Chronological session log
│
├── .claude/agents/                 # Native Claude Code subagents
├── .agents/rules/                  # Development rules
├── AI_AGENTS_SYSTEM/               # Agent authority documents
│
├── CLAUDE.md                       # Project instructions for Claude
└── .env.example                    # Environment variable template
```

---

## Public Interface Registry

> List every public function/class/endpoint that external modules may call.
> Update this section whenever a public interface is added, changed, or removed.

### [Entry Layer] Endpoints

```
POST /api/[resource]/        → create[Resource](request: [Resource]Request) → [Resource]Response
GET  /api/[resource]/{id}    → get[Resource](id: str) → [Resource]Response
PUT  /api/[resource]/{id}    → update[Resource](id: str, ...) → [Resource]Response
DELETE /api/[resource]/{id}  → delete[Resource](id: str) → SuccessResponse
```

### [Service Layer] Public API

```
[ServiceName].[method](param: Type) → ReturnType   # description
```

### [Data Layer] Public API

```
[RepositoryName].find_by_id(id: str) → Optional[Model]
[RepositoryName].save(model: Model) → Model
[RepositoryName].delete(id: str) → bool
```

---

## Module Dependency Graph

> Fill in after setup. Shows which modules import from which.

```
[entry_layer]
    → [service_layer]
        → [data_layer]
        → [core_layer]
    → [core_layer]

[frontend] → [backend] (HTTP/WebSocket only)
```

---

## Key Files Quick Reference

| File | Purpose |
|---|---|
| `CLAUDE.md` | Project instructions, tech stack, commands, domain knowledge |
| `AI_AGENTS_SYSTEM/AGENTS.md` | Agent rules and authority document |
| `AI_AGENTS_SYSTEM/FEATURE_INDEX.md` | Task → file navigation index |
| `.claude/agents/task-master.md` | Primary orchestrator |
| `.agents/rules/09-testing-and-reporting.md` | Test-first + HTML report protocol |
| `docs/sessions/_template.html` | Session report HTML template |
| `.env.example` | Required environment variables |
