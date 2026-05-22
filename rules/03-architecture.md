# Rule 03 — Architecture Rules

## Three-Layer Backend

```
Entry Layer (api/ | cmd/ | handlers/)
    → Validates input, calls services, returns responses.
    → NEVER contains business logic or data access.

Service Layer (service/ | domain/ | use_cases/)
    → Business logic, orchestration, domain rules.
    → NEVER contains I/O details (HTTP, DB, filesystem).

Core Layer (core/ | lib/ | shared/ | infrastructure/)
    → Data access, external integrations, shared utilities, config.
    → NEVER contains business logic.
```

> Update layer names in `CLAUDE.md` to match your project's actual directory structure.

## Module Boundaries

Each module owns its domain and exposes a clean public interface. No module reaches into another module's internals.

```python
# ALLOWED — calling public interface
user_service.get_user(user_id=123)

# FORBIDDEN — reaching into internals
user_service._cache["user:123"]
user_repository._db_connection.execute(...)
```

## Data Flow Rule

Data crosses layer boundaries only via typed contracts (dataclasses, Pydantic models, TypeScript interfaces). Never pass raw `dict` or untyped `object` between layers.

```python
# ALLOWED
def create_user(request: CreateUserRequest) -> UserResult: ...

# FORBIDDEN
def create_user(data: dict) -> dict: ...
```

## Async-First (if your stack uses async)

All I/O must be `async/await`. Blocking I/O on an async event loop is a critical bug.

```python
# FORBIDDEN — blocks event loop
def get_data():
    return requests.get(url)   # synchronous in async context

# ALLOWED
async def get_data():
    return await http_client.get(url)
```

## Frontend Architecture (if applicable)

```
Pages / Routes
    → Components
        → Services (API calls only)
            → Backend (HTTP / WebSocket)
```

- State management (Zustand / Redux / Pinia) for global state — avoid prop drilling
- All API calls in `services/` — never `fetch()`/`axios` directly in components
- All user-facing strings through i18n — never hardcoded

## Shared Logic Rule

When the same logic appears in two modules, extract it to a shared module on the second occurrence. Never duplicate — the third reader won't know which copy is canonical.
