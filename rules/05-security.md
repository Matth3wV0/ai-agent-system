# Rule 05 — Security Rules

## Secrets & Credentials

- NEVER log secret values (API keys, tokens, passwords, cookies) — log presence only: `api_key=PRESENT`
- NEVER commit secrets to source control — use `.env` files (gitignored) and environment variables
- NEVER store credentials in session data, JSON files, or database plaintext
- NEVER hardcode API keys, tokens, or passwords in source code — use config/env constants
- Always add `.env`, `*.key`, `*_secret*`, `credentials.*` to `.gitignore`

## Input Validation

- Validate ALL external input at system boundaries (HTTP request bodies, query params, CLI args, file uploads)
- Sanitize output to prevent injection: HTML (XSS), SQL, shell commands, Excel formula injection
- Strings starting with `=`, `+`, `-`, `@` in spreadsheet exports must be prefixed with `'` to prevent formula injection
- Never use `eval()`, `exec()`, or dynamic code execution

## Authentication & Authorization

- Check authorization on every protected endpoint — never rely on client-side checks alone
- Use parameterized queries / ORM methods — never string-concatenate SQL
- Rate-limit sensitive endpoints (login, password reset, API calls)
- Tokens and sessions must have expiry — never indefinite

## Data Handling

- Credentials and secrets → memory only, never persisted
- PII (email, phone, address) → log by ID only, never log the value
- Sensitive fields in API responses → return only what the caller needs (principle of least privilege)

## CORS & Network

- Restrict CORS to known frontend origins — never `*` in production
- No `eval` or `Function()` constructor from user-supplied strings in frontend
- Validate file uploads: check type, size, and scan content where applicable

## Escalate Immediately When

- A security vulnerability is discovered (don't fix silently — report first)
- A new external dependency with broad permissions is introduced
- Authentication or authorization logic changes
- PII handling rules change
