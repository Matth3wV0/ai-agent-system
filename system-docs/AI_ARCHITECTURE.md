# AI_ARCHITECTURE.md — AI Layer Inventory

> Single source of truth for all AI/ML components in this project.
> Read this BEFORE grepping for AI-related code — naive keyword search misses most of it.
> Fill in this template after setup. Keep it updated as AI components are added.

---

## §1 — How to Find AI Code (Grep Hints)

> Naive grep for model names misses code that uses wrappers or aliases.
> Use these patterns instead:

```bash
# Find all AI provider calls
grep -r "GenerativeModel\|openai\|anthropic\|ollama\|HfInference" src/ --include="*.py"

# Find all AI task invocations
grep -r "classify\|embed\|generate\|complete\|summarize" src/ai/ --include="*.py"

# Find prompt templates
find . -name "*.txt" -path "*/prompts/*"
find . -name "*.jinja" -o -name "*.j2"
```

> Update these patterns to match your actual AI libraries after setup.

---

## §2 — AI Zones Inventory

> Fill in each zone that applies to your project. Delete zones that don't apply.

### Zone 1 — AI Providers (`src/ai/providers/` or equivalent)

| Component | Purpose | Model/API |
|---|---|---|
| [ProviderClass] | [what it does] | [model name / API] |

Key files:
- `[path]` — [description]

### Zone 2 — AI Intelligence (`src/ai/intelligence/` or equivalent)

Classifiers, embeddings, sentiment, topic models:

| Component | Purpose | Triggers |
|---|---|---|
| [ClassifierName] | [what it classifies] | [when it runs] |

Key files:
- `[path]` — [description]

### Zone 3 — Core AI Utils (`src/core/ai/` or equivalent)

Shared AI infrastructure (prompt management, token counting, caching):

| Component | Purpose |
|---|---|
| [UtilName] | [what it provides] |

### Zone 4 — AI Scripts (`scripts/` or equivalent)

Offline analysis, training, evaluation scripts:

| Script | Purpose | Runs |
|---|---|---|
| `[script.py]` | [what it does] | [when/how] |

---

## §3 — Task Registry

> Maps business tasks to AI components. Fill in after setup.

| Task | Component | Model | Trigger |
|---|---|---|---|
| [e.g. classify content] | [ClassifierName] | [model] | [e.g. on ingest] |
| [e.g. generate summary] | [SummarizerName] | [model] | [e.g. on demand] |

---

## §4 — Known Duplicates & Anti-Patterns

> Document any duplicate AI code that exists and should be consolidated.
> Document patterns that have caused bugs.

### Duplicates (to consolidate)
- (none yet — fill in as discovered)

### Anti-Patterns (never do these)
- Calling AI providers directly from route handlers — always go through the AI layer
- Hardcoding model names in business logic — use config constants
- Blocking the async event loop with synchronous model inference
- Storing raw model responses without structured parsing

---

## §5 — Cost & Quota Notes

> Fill in rate limits, token budgets, and cost guards after setup.

| Provider | Rate Limit | Token Budget | Cost Guard |
|---|---|---|---|
| [Provider] | [requests/min] | [tokens/request] | [circuit breaker?] |

---

## §6 — Model Version Pins

> Pin model versions here so rotation is a one-place change.

```python
# src/ai/config.py (or equivalent)
MODELS = {
    "classifier": "[model-name-and-version]",
    "embedder":   "[model-name-and-version]",
    "generator":  "[model-name-and-version]",
}
```

---

## Last updated: [fill in after setup]
