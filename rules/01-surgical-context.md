# Rule 01 — Surgical Context Protocol

Before touching any code, follow this deterministic algorithm. No exceptions.

## The Algorithm

```
Step 1 — Parse the task into a Feature Address:
  <domain>::<module>::<submodule>::<action>

  Examples:
    "add user profile endpoint"  → api::users::profile::create
    "fix login error"            → service::auth::session::fix
    "show dashboard charts"      → ui::dashboard::charts::read
    "update email validation"    → core::validation::email::update

Step 2 — Look up files in AI_AGENTS_SYSTEM/FEATURE_INDEX.md (use the domain::module key)
Step 3 — If not found: check AI_AGENTS_SYSTEM/PROJECT_MAP.md directory tree
Step 4 — Read ONLY the required files:
           • Bug fix:        3 files max
           • New feature:    5-7 files max
           • Cross-module:   8-10 files max
Step 5 — Implement
Step 6 — After coding: update FEATURE_INDEX.md + PROJECT_MAP.md
```

**Rule: Never read a file not on the computed list. Never skip a file that is.**

**Rule: If you cannot understand what to build within the context budget → stop and ask for clarification.**

**Rule: If task spans 3+ domains → decompose into sub-tasks, each with its own Feature Address.**
