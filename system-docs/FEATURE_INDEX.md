# FEATURE_INDEX — [PROJECT_NAME]

> Navigation index for AI Agents. Given a task, search this file first.
> Every feature in the codebase has an entry here.
> Format: `domain::module::feature_name` — searchable by keyword.
> Never delete entries. Mark obsolete features `deprecated` and link to replacement.

---

## HOW TO USE THIS FILE

1. Parse your task into a Feature Address: `<domain>::<module>::<feature>`
2. Ctrl+F / grep for the domain or module keyword
3. The entry tells you exactly which files to open — nothing more
4. If no entry exists: the feature is new → create the entry as part of your work

---

## HOW TO ADD AN ENTRY

Copy this template and fill it in:

```markdown
## domain::module

**Status**: active | deprecated
**Owner module**: path/to/module/
**Primary files**:
  - path/to/main_file.py          # one-line role description
  - path/to/secondary_file.py     # one-line role description
**Interface files**:
  - path/to/shared.py             # imported by this module
**Test files**:
  - tests/test_module.py
**Depends on features**:
  - domain::other_module          # features this one calls
**Depended on by features**:
  - domain::consumer_module       # features that call this one
**Last modified**: YYYY-MM-DD
**Change summary**: One sentence describing what was added or changed.
```

---

<!-- Add entries below this line, grouped by domain -->

## ADDING NEW ENTRIES

When you implement a new feature, append an entry using the template above.
Keep entries grouped by domain, alphabetical within each domain.

Example first entry:

## api::health

**Status**: active
**Owner module**: src/api/
**Primary files**:
  - src/api/health.py             # GET /health endpoint + deep diagnostics
**Test files**:
  - tests/test_api_health.py
**Depends on features**: (none)
**Depended on by features**: (monitoring, CI readiness checks)
**Last modified**: YYYY-MM-DD
**Change summary**: Initial health check endpoint.
