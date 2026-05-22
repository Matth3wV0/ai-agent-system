# Rule 09 — Test-First & HTML Reporting Protocol (MANDATORY)

> Applies to: every coding session (this session + all future sessions).
> Owner: any agent that writes or edits code (engineer, task-master, claude).
> Failure to follow this rule = work is considered incomplete.

---

## 1. Test-First Loop (NON-NEGOTIABLE)

For **every** change that touches a source code file, the agent **must** complete this loop **before** moving to the next change:

```
1. WRITE the test FIRST (or before the same turn as the code)
     - File: tests/test_<feature>.py  (Python) or __tests__/<feature>.test.ts (TS)
     - At least 1 happy path + 1 failure mode + 1 regression guard
     - Test must actually exercise the new code (no `assert True` stubs)

2. RUN the test BEFORE declaring the task done
     - Use the project's test command (see CLAUDE.md)
     - The command output must be captured and stored (see §3)

3. INTERPRET the result
     - All green → continue to next phase
     - Any red → STOP, fix the code (not the test), re-run
     - DO NOT mark the task complete on partial pass

4. MEASURE performance regression
     - For any function in a hot path (request handler, data processor, background job):
         baseline_seconds = previous wall time on the same input
         new_seconds      = current wall time on the same input
       If new_seconds > 1.5 × baseline_seconds → flag in report, get user approval

5. RECORD all of the above in the session HTML report (see §3)
```

**Forbidden patterns:**
- Writing code without a corresponding test in the same session
- Filtering test runs so narrowly that broken tests get skipped
- Adding `xfail`/`skip` markers without a written justification in the report
- "Tests pass in CI but not locally" — local must pass before push
- Claiming "agent verified the design" without running actual tests

---

## 2. Manual Verification Handoff

When local testing cannot fully verify a feature (Docker, external API, hardware dependency), the agent **must**:

1. State *explicitly* in the report: "MANUAL VERIFICATION REQUIRED"
2. List the exact commands the human must run, in copy-paste form
3. State the *expected output* for each command
4. State the *actual output the agent expects to see based on its design*
5. Provide a *rollback command* if verification fails

Example (good):
```
MANUAL VERIFICATION REQUIRED — Docker service

Command:
    docker compose up -d

Expected output (last line):
    ✔ Container myapp_db   Started

If FAIL → rollback:
    docker compose down -v
```

---

## 3. HTML Session Report (MANDATORY)

For **every session that touches code**, the agent **must** generate one HTML report:

### 3.1 Location
```
docs/sessions/session_<YYYY-MM-DD>_<short-slug>.html
```
Slug example: `user_auth_fix`, `dashboard_charts`, `email_service`.

### 3.2 Required sections (in this order)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Session Report — {{date}} — {{slug}}</title>
  <style>... (use existing template if available) ...</style>
</head>
<body>
  <h1>Session Report — {{date}}</h1>

  <h2>1. Summary</h2>          <!-- one paragraph: goal + outcome -->
  <h2>2. Files Changed</h2>    <!-- table: path | action | LOC delta | rationale -->
  <h2>3. Tests Run</h2>        <!-- table: test file | passed | failed | time -->
  <h2>4. Performance Check</h2><!-- before/after timings for hot paths -->
  <h2>5. Manual Verifications</h2> <!-- what the user needs to run -->
  <h2>6. Known Issues / Deferred</h2> <!-- carried-over items -->
  <h2>7. Next Steps</h2>       <!-- recommended follow-up tasks -->
  <h2>8. Diff Excerpts</h2>    <!-- key code snippets, syntax highlighted -->
</body>
</html>
```

### 3.3 Template

A reusable template lives at: `docs/sessions/_template.html`.
If the template does not exist, the **first agent of any session** must create it from the structure above.

### 3.4 Index

`docs/sessions/INDEX.md` lists every report chronologically. Append a one-line entry:
```
- 2026-05-22 — User auth fix — [report](session_2026-05-22_user_auth_fix.html)
```

### 3.5 Delegation policy — MANDATORY (token economy)

**HTML report generation MUST NOT run on Opus.** This is mechanical document
authoring (templating, table-filling, diff excerpting) — no architectural
reasoning required. Delegate to a cheaper model:

| Task | Model | Agent |
|---|---|---|
| HTML session report | **Haiku** | `docs-keeper` |
| FEATURE_INDEX.md / PROJECT_MAP.md entries | **Haiku** | `docs-keeper` |
| `INDEX.md` append + cross-link updates | **Haiku** | `docs-keeper` |
| Module manifest writes | **Haiku** | `docs-keeper` |
| AI_ARCHITECTURE.md updates (cross-doc synthesis) | **Sonnet** | `docs-keeper` or `claude` |
| Architecture decision records, design docs | **Sonnet** | `architect` → `engineer` writes |

**Stays on Opus** (do NOT delegate):
- Test design (deciding what to assert)
- Root-cause analysis of test failures
- Architectural / API design decisions
- Code review of complex diffs

**How to delegate** (from Opus orchestrator):
```
Agent(
  description="HTML session report",
  subagent_type="docs-keeper",
  prompt="Fill the template at docs/sessions/_template.html with:
          - inventory of files changed (from git status + the diffs I'll list)
          - test output I already captured (paste verbatim)
          - manual verification commands the user must run
          Save to docs/sessions/session_<date>_<slug>.html.
          Do NOT decide what to test or what the fix should be — just write the doc."
)
```

**Anti-pattern**: orchestrator (Opus) writes the HTML itself line by line.
If the orchestrator finds itself emitting `<table>`/`<tr>`/`<td>` inside `Write` calls,
it MUST stop and re-delegate to a cheaper agent.

---

## 4. Rule Enforcement

### 4.1 Pre-finish checklist (every agent must self-audit)

- [ ] Did I write a test in this session for every code change?
- [ ] Did I actually RUN the test (paste output)?
- [ ] Are all tests green (no skip/xfail without justification)?
- [ ] Did I check performance vs previous baseline (where applicable)?
- [ ] Did I generate the HTML report?
- [ ] Did I append the report to `docs/sessions/INDEX.md`?
- [ ] Did I update `FEATURE_INDEX.md` and `PROJECT_MAP.md`?

Any NO → task is NOT complete. Keep working.

### 4.2 Exemptions (limited)

Test-first does NOT apply to:
- Pure markdown / `.md` documentation changes
- Comment-only edits
- Whitespace / formatting reflows that don't change AST
- One-line typo fixes (still record in report)

Everything else: test required.

---

## 5. Why This Rule Exists

Silent failures are the costliest class of bug — they pass review, pass CI, and only surface in production. The test-first + HTML report loop catches three recurring failure modes:

1. **The stub that passes**: an agent writes `assert result is not None` and declares success — but the function returns `None` on error, which is exactly the case that needs testing.
2. **The untested retry**: a function works on attempt 1 but fails silently on retry — no test covers the retry path.
3. **The missing rollback**: a migration succeeds but has no documented rollback — when it fails in production, the recovery procedure doesn't exist.

The HTML report forces every session to produce a permanent artifact that answers: what changed, did it work, and what does the user need to do manually?

---

## 6. HTML Quality Standards

> Source: *"The Unreasonable Effectiveness of HTML"* — Thariq Shihipar (Anthropic Claude Code team)
> Key thesis: HTML is categorically superior to Markdown for session reports.

### 6.1 Why HTML beats Markdown for reports

| Capability | Markdown | HTML |
|---|---|---|
| Tables with styling | Basic | Full CSS, color-coded |
| Visual hierarchy | Headings only | Cards, pills, status bars, tabs |
| Interactivity | None | Tabs, collapsibles, copy buttons |
| Diagrams | ASCII art | SVG, positioned elements |
| Sharing | Attachment needed | Upload → link |
| Long-form readability | Degrades past ~100 lines | Tabs keep it navigable |

### 6.2 Mandatory patterns in every session report

**Tabs** — split sections (Summary / Tests / Files / Diffs / Manual / Next) so the report stays navigable regardless of size.

**Status bar** — top-of-page grid of key numbers: tests passed, tests failed, files changed, LOC delta, perf status. Readable at a glance without scrolling.

**Copy buttons** on every command block — the `<pre>` + `<button onclick="copyCmd(...)">` pattern. Manual verification steps must be one-click copyable.

**Color-coded test table** — `<span class="pill ok">PASS</span>` / `<span class="pill fail">FAIL</span>` — not plain text.

**Collapsible diff blocks** — `<details><summary>filename</summary><pre>diff</pre></details>` — available but doesn't dominate.

**Alert boxes** for MANUAL VERIFICATION REQUIRED — `<div class="alert warn">` with explicit rollback command.

### 6.3 Forbidden patterns in HTML reports

- Writing the full report as a `<pre>` dump — that's Markdown in an HTML wrapper
- Using only `<h2>` + `<p>` structure with no visual hierarchy
- Emitting raw test output inline without a `<details>` collapse
- Skipping the status bar — forces reader to skim entire document for pass/fail
- Generating the HTML on Opus (§3.5 still applies — delegate to docs-keeper)

### 6.4 When to add richer elements

- **SVG diagrams**: data flow changed, new module wired in, architecture decision made
- **Interactive sliders/toggles**: design exploration or prototype sessions only
- **Grid comparisons**: multiple approaches were considered — show side by side

Standard bug-fix / feature sessions: tabs + status bar + copy buttons is sufficient.

---

## 7. Versioning

| Version | Date | Change |
|---|---|---|
| 1.0 | 2026-05-22 | Initial portable release — genericized from Facebook Scrape V7 production system |
