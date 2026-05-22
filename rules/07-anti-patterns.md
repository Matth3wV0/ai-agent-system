# Rule 07 — Anti-Patterns (PROHIBITED — block merge)

These patterns are never acceptable. Violating any of them is a critical issue.

## Code Structure

- **`_backup`, `_old`, `_v2` files** — modify the canonical file; use git for history
- **Functions > 60 lines** — decompose; long functions hide complexity and fail review
- **Circular imports** — restructure so the dependency graph is a DAG
- **Duplicate logic** — extract to shared module on second occurrence; never copy-paste
- **Raw `dict` / untyped object across module boundaries** — define a typed model
- **`except Exception: pass`** — always re-raise or log and raise; silent failures are invisible bugs

## Logging & Observability

- **`print()` / `console.log()` in production code** — use structured logger
- **Unstructured log messages** — `logger.info(f"Got {n} items")` is unsearchable; use `extra={}`
- **Logging secret values** — log presence/ID only, never the value itself

## I/O & Async

- **Synchronous blocking I/O on an async event loop** — use `await` and async clients
- **Polling when a push mechanism exists** — use WebSocket / SSE / callbacks; polling wastes resources and adds latency
- **Hardcoded file paths** — use config constants or environment variables

## API & Frontend

- **Business logic in route handlers** — handlers validate + delegate; they do not compute
- **Direct `fetch()` / `axios` in UI components** — all requests via a services layer
- **Hardcoded user-facing strings** — all strings through i18n; hardcoded strings break localization
- **`any` type in TypeScript** — define a proper interface; `any` defeats the type system
- **`float` for count/quantity fields** — use `int`; floats accumulate rounding errors in aggregations

## Data & Persistence

- **Secrets or credentials in source files, session JSON, or database plaintext** — use env vars + secrets manager
- **Constructing or parsing opaque tokens / cursors** — treat as black-box strings; forward them verbatim
- **Mutable shared state between concurrent requests** — use per-request context or thread-safe structures

## Dependencies

- **New external dependency without justification** — every new dependency is a supply-chain risk; justify in PR
- **Pinning to `latest` or `*`** — pin exact versions in production; unpinned dependencies break reproducibility
