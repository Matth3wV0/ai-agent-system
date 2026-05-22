# MODULE_MANIFEST Template & Examples

> This file shows how to write a MODULE_MANIFEST.md for each module.
> Copy the template below, fill it in, and place it at the root of the module directory.
> Maximum 80 lines per manifest. If you need more, the module is too large — split it.

---

## Why MODULE_MANIFEST.md Exists

When an AI agent receives a task like "fix the user authentication flow", it should
be able to read ONE file per affected module and know:
  - What the module does (skip reading 6 source files to understand this)
  - What its public API is (skip reading headers to find this)
  - What patterns are in use (skip discovering async patterns, error handling, etc.)
  - What the known quirks are (skip making a mistake someone already documented)

This file is the difference between a 5-file context load and a 25-file context load.

---

## Template

```markdown
# MODULE_MANIFEST — <module_path>

## Purpose
<2–3 sentences. What problem does this module solve? What is it NOT responsible for?>

## Public API
<List every function/class that external modules may call. Include signature + one-line description.>
  function_name(param: Type, ...) → ReturnType   # description

## Internal Structure
  filename.py    — one-line description of what lives here

## Imports from other modules
  <module>.<submodule>   — which types/functions are used

## Patterns in use
  - <pattern name>: <one-line explanation of how it's applied here>

## Known constraints / gotchas
  - <anything that will cause bugs if the next agent doesn't know it>

## Performance characteristics
  - <latency targets, throughput notes, memory constraints if relevant>

## Last updated: YYYY-MM-DD
```

---

## Example: Generic Service Module

```markdown
# MODULE_MANIFEST — src/services/user/

## Purpose
Handles all user account business logic: creation, authentication, profile updates,
and password management. Does NOT handle HTTP request/response shapes (that's api/users.py)
or database queries (that's repositories/user_repo.py).

## Public API
  UserService.create(data: CreateUserData) → User          # Create and persist new user
  UserService.authenticate(email, password) → AuthToken    # Validate credentials, return token
  UserService.get_by_id(user_id: str) → Optional[User]    # Fetch user or None
  UserService.update_profile(user_id, data: ProfileData) → User  # Update mutable profile fields
  UserService.change_password(user_id, old_pw, new_pw) → bool    # Validate old, set new

## Internal Structure
  service.py        — UserService class (all public methods above)
  validators.py     — Email format, password strength, display name rules
  password.py       — bcrypt hashing + verification helpers (no business logic)

## Imports from other modules
  repositories.user_repo   — UserRepository for persistence
  core.email               — send_verification_email(), send_reset_email()
  core.config              — PASSWORD_MIN_LENGTH, TOKEN_EXPIRY_HOURS

## Patterns in use
  - Dependency injection: UserService receives UserRepository in __init__, not singleton
  - Explicit errors: raises UserNotFoundError, InvalidCredentialsError, WeakPasswordError
  - No I/O in validators: validation functions are pure, take strings, return bool

## Known constraints / gotchas
  - change_password() requires the OLD password — it is not an admin reset function
  - create() sends a verification email; in tests, mock core.email or the email will fire
  - authenticate() returns None (not raises) when credentials are wrong — callers must check

## Performance characteristics
  - authenticate(): ~100ms (bcrypt is intentionally slow; do not optimize)
  - get_by_id(): <5ms with warm cache, ~20ms cold (single DB query)

## Last updated: YYYY-MM-DD
```

---

## Example: Generic API Layer Module

```markdown
# MODULE_MANIFEST — src/api/users/

## Purpose
HTTP entry point for all user-related operations. Validates request shapes,
calls UserService, and returns typed responses. Contains NO business logic —
if it's doing more than validate + delegate + serialize, move it to UserService.

## Public API (HTTP endpoints)
  POST   /api/users/           → create_user(body: CreateUserRequest) → UserResponse
  GET    /api/users/{id}       → get_user(id: str) → UserResponse
  PUT    /api/users/{id}       → update_user(id: str, body: UpdateUserRequest) → UserResponse
  POST   /api/users/login      → login(body: LoginRequest) → TokenResponse
  POST   /api/users/logout     → logout(token: str) → SuccessResponse

## Internal Structure
  router.py     — FastAPI/Express router with all route definitions
  schemas.py    — Request/response Pydantic models (CreateUserRequest, UserResponse, etc.)
  deps.py       — Dependency injectors (get_current_user, require_admin)

## Imports from other modules
  services.user   — UserService (all business logic goes here)
  core.auth       — verify_token(), get_current_user()

## Patterns in use
  - Thin handlers: every handler is ≤15 lines; delegate to service immediately
  - Schema validation: Pydantic models on every request; FastAPI rejects invalid input at boundary
  - Dependency injection: current_user injected via FastAPI Depends()

## Known constraints / gotchas
  - 422 errors are automatic from Pydantic — do not catch them in handlers
  - Auth is checked via deps.py inject, not in handler body — don't add manual checks
  - UserResponse deliberately omits password_hash — never add it

## Last updated: YYYY-MM-DD
```

---

## Checklist for Creating a New MODULE_MANIFEST.md

```
□ File placed at module root directory
□ Purpose: 2-3 sentences, includes what it does AND what it's NOT responsible for
□ Public API: complete list of externally-callable functions with signatures
□ Internal Structure: every file in the module listed with one-line description
□ Imports: explicit list of cross-module dependencies
□ Patterns: non-obvious patterns documented
□ Gotchas: anything that will cause bugs if the next agent doesn't know it
□ Under 80 lines total
□ Last updated date set
```
