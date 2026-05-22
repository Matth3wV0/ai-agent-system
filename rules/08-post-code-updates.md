# Rule 08 — Post-Code Update Obligations

After every coding session, before closing. No exceptions.

## MANDATORY (every session that touches code)

- **`AI_AGENTS_SYSTEM/FEATURE_INDEX.md`** — add/update entry for every touched feature
- **`AI_AGENTS_SYSTEM/PROJECT_MAP.md`** — update interface registry if any public signature changed
- **`MODULE_MANIFEST.md`** in the affected module — update if module behavior or dependencies changed

## CONDITIONAL (only when the specific thing changed)

| Condition | File to update |
|---|---|
| New environment variable added | `.env.example` |
| New TypeScript types added | `types/index.ts` (or equivalent types file) |
| New user-facing strings added | i18n locale files (all languages) |
| New configurable parameter added | config file / schema |
| New API endpoint added or removed | API documentation or OpenAPI spec |
| New dependency added | `requirements.txt` / `package.json` + justification in commit message |

## Pre-Finish Checklist (self-audit before declaring complete)

- [ ] Test written for every code change in this session?
- [ ] Test actually RUN (output captured)?
- [ ] All tests green (no skip/xfail without written justification)?
- [ ] Performance vs previous baseline checked (where applicable)?
- [ ] HTML session report generated at `docs/sessions/session_<date>_<slug>.html`?
- [ ] `docs/sessions/INDEX.md` updated with one-line entry?
- [ ] `AI_AGENTS_SYSTEM/FEATURE_INDEX.md` updated?
- [ ] `AI_AGENTS_SYSTEM/PROJECT_MAP.md` updated (if public interface changed)?

Any NO → task is NOT complete. Keep working.

## Escalate to User When

- Any change to authentication or authorization logic
- Introducing a new external dependency
- Architectural change affecting 3+ modules
- Security-relevant issue discovered
- Breaking change to a public API
- Data migration required
