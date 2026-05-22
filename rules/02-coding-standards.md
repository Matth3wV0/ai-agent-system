# Rule 02 — Coding Standards (Non-Negotiable)

## Forbidden Patterns

- No filename suffixes: `_fixed`, `_v2`, `_enhanced`, `_new`, `_old`, `_backup`
- No mock implementations in the production path
- No inline comments unless the WHY is non-obvious (hidden constraint, workaround, subtle invariant)
- No hardcoded secrets, tokens, API keys, or credentials anywhere in source code
- No `print()` / `console.log()` in production code — use structured logger exclusively
- No `except Exception: pass` or swallowed errors — always re-raise or log and raise
- No function > 60 lines — decompose into smaller units
- No circular imports — module dependency graph must be a DAG
- No duplicate logic across modules — extract to shared on second occurrence
- No raw `dict` / untyped `object` crossing module boundaries — use typed models

## Python Naming

- Types/classes: `PascalCase` (`UserService`, `SessionData`)
- Functions/variables: `snake_case` (`get_user`, `current_status`)
- Constants: `SCREAMING_SNAKE_CASE` (`MAX_RETRIES`, `DEFAULT_TIMEOUT`)
- Private internals: `_snake_case` prefix (`_normalize_cursor`)
- Absolute imports from project root
- Type hints required on all function signatures

## TypeScript Naming

- Components: `PascalCase` (`UserProfile`, `DashboardCard`)
- Hooks: `camelCase` with `use` prefix (`useWebSocket`, `useForm`)
- Services: `camelCase` with `Api` suffix (`userApi`, `sessionApi`)
- Types/Interfaces: `PascalCase` (`UserData`, `ApiResponse`)
- Stores: `camelCase` with `Store` suffix (`useSettingsStore`)
- All shared types in `types/index.ts` — single source of truth
- No `any` type unless interfacing with untyped third-party code

## Structured Logging

```python
# Correct — structured, contextual, searchable
logger.info("user_created", extra={"user_id": user.id, "email_domain": domain})
logger.error("payment_failed", extra={"order_id": order.id, "error": type(e).__name__})

# Wrong — unstructured, unsearchable
logger.info(f"Created user {user.id}")
print("Payment failed!")
```

## Commit Message Format

```
<type>(<scope>): <imperative summary>

Types: feat | fix | refactor | perf | test | docs | chore | security
Scope: module name (users, auth, dashboard, api, core)

Examples:
  feat(users): add profile image upload endpoint
  fix(auth): handle expired token refresh race condition
  refactor(core): extract email validation to shared utility
```
