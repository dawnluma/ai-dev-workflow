# Stage 2 — Idea-to-Ticket MVP Implementation Plan

This document describes the smallest sensible architecture for **Stage 2** of the `ai-dev-workflow` repository.

**Naming:**

| Use | Form |
|-----|------|
| Human-facing concept, docs, workflow name | **Idea-to-Ticket** |
| Python package, folder on disk, imports | **`idea_to_ticket`** |

Stage 2 delivers the full local pipeline, including **one live AI provider** (Stage 2B). It runs on a developer machine and does **not** integrate with GitHub or production systems.

```text
Raw Idea
   ↓
AI
   ↓
Structured Ticket Draft
   ↓
Schema Validation
   ↓
Readable Ticket
```

### Stage 2 sub-phases

| Phase | Goal |
|-------|------|
| **Stage 2A — Local Core** | Schema, validation, renderer, pipeline, `FakeDrafter`, fixture CLI, automated tests (no real API). |
| **Stage 2B — Live AI Integration** | One approved provider: prompt + raw idea → model JSON → validate → readable ticket. |

**Stage 2 is not complete after Stage 2A alone.** Stage 2B and the manual live end-to-end check are required for Stage 2 sign-off.

### Out of scope for Stage 2 (unchanged)

Stage 2 must **not** implement:

- GitHub Issue search
- GitHub Issue creation
- GitHub Project automation
- GitHub duplicate checking
- Multi-agent orchestration
- Pull request automation
- Production project integration

---

## Table of Contents

- [1. Context](#1-context)
- [2. Files to Create](#2-files-to-create)
- [3. Responsibility of Each File](#3-responsibility-of-each-file)
- [4. Minimum Python Dependencies](#4-minimum-python-dependencies)
- [5. How the CLI Works (`idea_to_ticket.idea`)](#5-how-the-cli-works-idea_to_ticketidea)
- [6. Structured Ticket Representation](#6-structured-ticket-representation)
- [7. Schema Validation](#7-schema-validation)
- [8. Readable Ticket Generation](#8-readable-ticket-generation)
- [9. AI Provider Boundary](#9-ai-provider-boundary)
- [10. Testing Strategy](#10-testing-strategy)
- [11. Implementation Order](#11-implementation-order)
- [12. Documentation Updates (Required in Stage 2)](#12-documentation-updates-required-in-stage-2)
- [13. Decisions Requiring Human Approval](#13-decisions-requiring-human-approval)
- [14. Stage 2 Definition of Done](#14-stage-2-definition-of-done)

---

## 1. Context

At the time of this plan, the repository has completed **Stage 1** (foundation):

- `README.md`, `AGENTS.md`, `CLAUDE.md`, `.env.example`, `.gitignore`
- Empty placeholder folders: `idea-to-ticket/` (legacy name from Stage 1), `prompts/`, `schemas/`, `tests/`
- `examples/` does not exist yet

**Stage 2 renames the application package folder** from `idea-to-ticket/` to `idea_to_ticket/` and adds `idea_to_ticket/__init__.py` so it is a normal importable package.

The structured ticket fields, ticket types, and priorities are already documented in `README.md` and `AGENTS.md`. Stage 2 implements that contract locally.

The field `duplicate_of` remains on the schema so the shape matches the long-term ticket model, but **Stage 2 requires it to be `null`** (no duplicate checking in this stage).

---

## 2. Files to Create

```text
ai-dev-workflow/
├── pyproject.toml
├── prompts/
│   └── idea_intake.md
├── schemas/
│   ├── __init__.py
│   └── ticket.py
├── idea_to_ticket/
│   ├── __init__.py
│   ├── idea.py
│   ├── pipeline.py
│   ├── provider.py
│   └── render.py
├── examples/
│   ├── sample-idea.md
│   └── sample-ticket.json
└── tests/
    ├── test_ticket_schema.py
    ├── test_render.py
    └── test_pipeline.py
```

**Stage 2 repository layout change (during implementation):**

- Remove or stop using the empty Stage 1 folder `idea-to-ticket/` after `idea_to_ticket/` exists (single application package; do not keep both).

**Intentionally not created in Stage 2:**

- Root-level `idea.py`
- `orchestrator/` implementation
- GitHub client, duplicate checker, or any GitHub write/search automation
- A second or third AI provider implementation (one approved provider only)
- `[project.scripts]` console entry (optional later; not required for Stage 2)

**Packaging:** Declare `idea_to_ticket` and `schemas` in `pyproject.toml` with standard package discovery (for example `[tool.setuptools.packages.find]` with `where = ["."]`). Tests import normally:

```python
from schemas.ticket import TicketDraft
from idea_to_ticket.pipeline import parse_and_validate
from idea_to_ticket.render import render_ticket
```

No pytest `pythonpath` workaround is required.

**Primary CLI (package module entry):**

```bash
python -m idea_to_ticket.idea
```

`idea_to_ticket/idea.py` must define `main()` and guard execution with `if __name__ == "__main__": main()` so `python -m idea_to_ticket.idea` works from the repository root (with the project installed editable or on `PYTHONPATH` as configured in `pyproject.toml`).

---

## 3. Responsibility of Each File

| File | Responsibility |
|------|----------------|
| `pyproject.toml` | Python 3.12+, `pydantic`, `pytest`; Stage 2B adds **only** dependencies required by the **one** chosen provider (see §4). Standard package discovery. |
| `prompts/idea_intake.md` | Version-controlled prompt: return one JSON object matching ticket fields; lowercase `type` and `priority`; `duplicate_of` must be `null`. Used by live AI in Stage 2B. |
| `schemas/ticket.py` | `TicketType`, `Priority`, and `TicketDraft` — canonical schema. |
| `schemas/__init__.py` | Re-export `TicketDraft`. |
| `idea_to_ticket/__init__.py` | Package marker; optional version string. |
| `idea_to_ticket/provider.py` | `complete(system_prompt, idea) -> str` protocol; `FakeDrafter` (2A); `load_drafter()`; **one** live provider class (2B). |
| `idea_to_ticket/pipeline.py` | Parse model text (optional fence), `json.loads`, `TicketDraft.model_validate`. |
| `idea_to_ticket/render.py` | Validated `TicketDraft` → human-readable text. |
| `idea_to_ticket/idea.py` | CLI via `main()`: idea input, drafter, validate, render; `--idea`, `--fixture`, `--json`. |
| `examples/sample-idea.md` | Sample raw idea for manual live runs (2B). |
| `examples/sample-ticket.json` | Sample structured ticket; fixture for tests and `--fixture`. |
| `tests/test_ticket_schema.py` | Schema acceptance and rejection cases. |
| `tests/test_render.py` | Readable layout from a known draft. |
| `tests/test_pipeline.py` | Pipeline + `FakeDrafter`; no network. |

---

## 4. Minimum Python Dependencies

### Stage 2A

**Runtime:**

- `pydantic`

**Development:**

- `pytest`

Use the standard library for CLI, JSON, and paths where possible.

### Stage 2B (add when implementing the approved provider)

Add **only** what that single provider needs, for example:

- Official SDK for the chosen vendor, **or**
- `httpx` (or similar) if using a minimal HTTP client

Do **not** add SDKs or clients for providers that are not the one approved implementation.

Optional for local config (human decision): `python-dotenv` to load `.env` for API keys — only if adopted for Stage 2B.

Still **not** required for Stage 2:

- Multiple provider packages
- GitHub / `gh` libraries for workflow automation
- Orchestration frameworks

`ruff` and `mypy` can wait until after Stage 2 is complete unless you choose otherwise.

---

## 5. How the CLI Works (`idea_to_ticket.idea`)

From the repository root (after `pip install -e .` or equivalent):

```bash
python -m idea_to_ticket.idea
```

**End-to-end flow (live path, Stage 2B):**

1. Print `What is your idea?` and read one line (unless `--idea` is passed).
2. Reject empty or whitespace-only input before calling any drafter (exit code `1`).
3. Load `prompts/idea_intake.md`.
4. Call `load_drafter()` → `complete(system_prompt, raw_idea)` → model text string.
5. `idea_to_ticket.pipeline` parses JSON from that string.
6. `TicketDraft.model_validate(...)` validates the object.
7. `idea_to_ticket.render` formats the validated draft.
8. Print the readable ticket to stdout (exit code `0`).

On JSON parse failure or schema validation failure: print a short error; exit `1`. Do not print a readable ticket after failed validation.

**Stage 2A / local modes (no real API):**

```bash
python -m idea_to_ticket.idea --fixture examples/sample-ticket.json
python -m idea_to_ticket.idea --idea "I want to investigate market breadth for MarketLens."
```

- `--fixture` — skip the drafter; load JSON from disk; validate and render (core path for 2A verification).
- `--idea` — non-interactive input; in 2A without a configured provider, expect a clear “provider not configured” style error after reading the idea (no invented ticket).

Optional: `--json` prints the validated object instead of the readable block (default remains readable).

---

## 6. Structured Ticket Representation

Single Pydantic model `TicketDraft` in `schemas/ticket.py`, aligned with `README.md` §23 and `AGENTS.md` §6:

| Field | Type / constraint |
|-------|-------------------|
| `title` | `str`, non-empty |
| `type` | Enum: `feature`, `bug`, `research`, `experiment`, `data`, `architecture`, `refactor`, `documentation` |
| `project` | `str`, non-empty |
| `priority` | Enum: `now`, `next`, `later`, `parked` |
| `labels` | `list[str]`, may be empty |
| `problem` | `str`, non-empty |
| `hypothesis` | `str`, may be empty |
| `related_context` | `str`, may be empty |
| `duplicate_of` | `null` only in Stage 2 |
| `missing_information` | `list[str]`, may be empty |
| `requires_experiment` | `bool` |
| `ready_for_development` | `bool` (display only in Stage 2) |
| `recommended_action` | `str`, non-empty |
| `acceptance_criteria` | `list[str]`, may be empty |

**Normalization:** Accept uppercase enum input from the model (e.g. `RESEARCH`) and store lowercase. Reject unknown values. Reject extra JSON keys (`extra = "forbid"`).

**Example** (`examples/sample-ticket.json`):

```json
{
  "title": "Research Market Regime Detection",
  "type": "research",
  "project": "MarketLens",
  "priority": "next",
  "labels": [
    "research",
    "algorithm",
    "market-regime"
  ],
  "duplicate_of": null,
  "problem": "The scanner does not currently include an overall market regime filter.",
  "hypothesis": "A market regime filter may reduce poor-quality signals during weak markets.",
  "related_context": "",
  "missing_information": [],
  "requires_experiment": true,
  "ready_for_development": false,
  "recommended_action": "Create research ticket",
  "acceptance_criteria": []
}
```

---

## 7. Schema Validation

Validation is **Pydantic on parsed JSON**, not a separate JSON Schema file and not an automatic retry loop.

**Pipeline steps** (`idea_to_ticket.pipeline`):

1. Strip optional single Markdown code fence around JSON if present.
2. `json.loads` — failure → user-facing parse error.
3. `TicketDraft.model_validate(data)` — failure → field-level validation error.

Only a validated `TicketDraft` is passed to `idea_to_ticket.render`.

**Stage 2 rule:** `duplicate_of` must be `null`. Any other value fails validation.

---

## 8. Readable Ticket Generation

`idea_to_ticket.render` is deterministic and testable. The LLM does not produce this view.

**Example shape:**

```text
Title:
Research Market Regime Detection

Type:
RESEARCH

Priority:
NEXT

Project:
MarketLens

Labels:
research, algorithm, market-regime

Problem:
...

Hypothesis:
...

Related Context:
...

Missing Information:
- ...

Requires Experiment:
Yes

Ready for Development:
No

Recommended Action:
Create research ticket

Acceptance Criteria:
- ...
```

**Rendering rules:**

- Display `type` and `priority` in uppercase in the readable view.
- Empty lists → `(none)` or as fixed in tests.
- Booleans → `Yes` / `No`.
- Omit `duplicate_of` when null.

---

## 9. AI Provider Boundary

**Protocol** (`idea_to_ticket/provider.py`):

```text
complete(system_prompt: str, idea: str) -> str
```

Return value is **raw model text**. Parsing and validation stay in `idea_to_ticket.pipeline`.

| Piece | Phase | Purpose |
|-------|-------|---------|
| `FakeDrafter` | 2A | Injected responses in unit tests. |
| `load_drafter()` | 2A stub / 2B live | 2A: clear error if live provider not configured; 2B: returns the **one** implemented provider. |
| Single live provider class | 2B | Calls approved API with `prompts/idea_intake.md` + user idea; returns model text. |

**Rules:**

- Implement **exactly one** approved provider for Stage 2 MVP (no multi-provider matrix).
- Provider and model selection remain a **human decision** until work starts on Stage 2B; 2A can proceed without that decision.
- `.env.example` documents `LLM_PROVIDER`, `LLM_MODEL`, and relevant API key placeholders; Stage 2B reads configuration via environment variables (and optionally `python-dotenv` if adopted).
- Do not add OpenAI + Anthropic + xAI “just in case”; add only the chosen provider’s integration.

Prompts, pipeline, and renderer stay provider-agnostic.

---

## 10. Testing Strategy

### Automated tests (Stage 2A — no real AI API)

Run from repository root with normal package imports (`pip install -e .` or equivalent):

| Area | What to test |
|------|----------------|
| Schema | Sample ticket validates; bad enums, missing fields, empty title, extra keys, non-null `duplicate_of` fail. |
| Normalization | Uppercase type/priority normalize to lowercase. |
| Render | Stable readable output for a fixed `TicketDraft`. |
| Pipeline | Clean JSON and fenced JSON succeed; garbage fails; empty idea never calls drafter. |
| Provider loader (2A) | `load_drafter()` fails clearly when live provider not configured. |
| Fixture CLI | `python -m idea_to_ticket.idea --fixture examples/sample-ticket.json` validates and renders. |

CI and day-to-day development rely on these tests; they must not require network or API keys.

### Manual verification (Stage 2B — required for Stage 2 completion)

After the single live provider is implemented, a human runs one successful end-to-end test:

```text
Raw Idea
→ Real AI
→ Structured Ticket
→ Schema Validation
→ Readable Ticket
```

Example:

```bash
python -m idea_to_ticket.idea --idea "<raw idea from examples/sample-idea.md or similar>"
```

Record outcome in the implementation PR or completion note (pass/fail, provider/model used, no secrets). Automated tests do not replace this manual live check for Stage 2 sign-off.

---

## 11. Implementation Order

### Stage 2A — Local Core

1. Rename `idea-to-ticket/` → `idea_to_ticket/`; remove legacy folder.
2. Add `pyproject.toml` (`pydantic`, `pytest`, package discovery).
3. Add `idea_to_ticket/__init__.py`.
4. Implement `schemas/ticket.py` and `examples/sample-ticket.json`.
5. Add `tests/test_ticket_schema.py`; make them pass.
6. Implement `idea_to_ticket/render.py` and `tests/test_render.py`.
7. Implement `idea_to_ticket/pipeline.py` and `tests/test_pipeline.py` with `FakeDrafter`.
8. Add `prompts/idea_intake.md`.
9. Add `idea_to_ticket/provider.py` (protocol, `FakeDrafter`, `load_drafter()` not configured for live use yet).
10. Add `idea_to_ticket/idea.py` with `main()`, `--idea`, `--fixture`, `--json`.
11. Add `examples/sample-idea.md`.
12. Run `pytest` and `python -m idea_to_ticket.idea --fixture examples/sample-ticket.json`.

**Checkpoint:** 2A is done when automated tests pass and the fixture CLI works. **This is not Stage 2 complete.**

### Stage 2B — Live AI Integration

13. Human approves **one** provider and model.
14. Add minimal dependencies for that provider only; update `.env.example` if needed (no real secrets).
15. Implement one live drafter class and wire `load_drafter()` for that provider only.
16. Verify prompt loading + `complete(prompt, idea)` returns text that pipeline can validate.
17. Manual end-to-end run: raw idea → real AI → validated structured ticket → readable output.
18. Update documentation per §12.

**Stop** after Stage 2B and doc updates. Do not start Stage 3 (GitHub) or other out-of-scope items.

---

## 12. Documentation Updates (Required in Stage 2)

When Stage 2 is implemented, update repository structure references from `idea-to-ticket/` to `idea_to_ticket/` wherever they refer to the **Python package / folder on disk**.

**Minimum files to update:**

- `README.md` (structure, usage CLI example)
- `AGENTS.md` (structure sections)
- Relevant `docs/setup/` guides (including v4 fresh setup guide §5 paths if they list the old folder)

**Keep unchanged for human readers:**

- Workflow name **Idea-to-Ticket**
- Conceptual descriptions of the Idea-to-Ticket workflow

**CLI in docs** should show:

```bash
python -m idea_to_ticket.idea
```

Doc updates are part of Stage 2 completion, not a deferred follow-up.

---

## 13. Decisions Requiring Human Approval

| # | Decision | Notes |
|---|----------|--------|
| 1 | **AI provider and model (Stage 2B)** | Required before live integration; 2A does not need it. Exactly **one** provider for MVP. |
| 2 | **Dependency manifest** | `pyproject.toml` only vs also `requirements.txt`. |
| 3 | **`--fixture` in 2A** | Required for local verification without API. |
| 4 | **`duplicate_of` must be null** | Until Stage 3+ duplicate semantics. |
| 5 | **`ready_for_development`** | Display only in Stage 2. |
| 6 | **Default CLI output** | Readable ticket; `--json` optional. |
| 7 | **`project` field** | From model output; `DEFAULT_PROJECT` optional fallback (decide in implementation). |
| 8 | **`python-dotenv` in 2B** | Optional convenience for local `.env`. |
| 9 | **Lint / type check** | Defer or include with Stage 2 implementation. |
| 10 | **Editable install** | `pip install -e .` recommended for `python -m idea_to_ticket.idea` and pytest. |

---

## 14. Stage 2 Definition of Done

Stage 2 is complete only when **Stage 2A, Stage 2B, documentation updates, and the manual live test** are all satisfied.

```text
Application package at idea_to_ticket/ with __init__.py
README.md, AGENTS.md, and relevant setup docs use idea_to_ticket/ for the package path; workflow name remains Idea-to-Ticket
pytest passes (schema, render, pipeline, provider loader behaviour) without network
Fixture CLI works: python -m idea_to_ticket.idea --fixture examples/sample-ticket.json
One approved AI provider implemented (not multiple)
Manual end-to-end success:
  Raw Idea → Real AI → Structured Ticket → Schema Validation → Readable Ticket
No GitHub Issue search or creation
No GitHub Project automation
No GitHub duplicate checking
No multi-agent orchestration
No PR automation
No production project integration
```

---

## Related Documentation

- [README.md](../../README.md) — §13 Stage 2, §23 Structured Ticket Model
- [AGENTS.md](../../AGENTS.md) — Idea-to-Ticket rules, structured output, stage boundaries
- [CLAUDE.md](../../CLAUDE.md) — Stage-scoped implementation behaviour
- [AI Development Workflow Fresh Setup Guide (v4)](./ai-development-workflow-fresh-setup-guide-v4.md) — §5 Stage 2 — Idea-to-Ticket v0
