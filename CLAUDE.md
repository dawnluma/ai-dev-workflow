# CLAUDE.md

## Purpose

This file is the Claude Code entry point for the `ai-dev-workflow` repository.

Claude Code must use this file together with the repository-wide rules in:

```text
AGENTS.md
```

Do not treat `CLAUDE.md` as a replacement for `AGENTS.md`.

The intended relationship is:

```text
CLAUDE.md
    ↓
Claude Code-specific instructions

AGENTS.md
    ↓
Repository-wide rules for all AI agents
```

---

## 1. Read These Files First

Before making changes, read:

```text
AGENTS.md
README.md
```

If the current task uses the AI workflow artefacts, also read:

```text
.ai/task.md
```

and any relevant existing files such as:

```text
.ai/plan.md
.ai/test-report.md
.ai/review.md
```

Only read additional documentation when it is relevant to the current task.

Useful documentation may exist under:

```text
docs/setup/
docs/explanations/
docs/architecture/
```

---

## 2. Claude Code's Primary Role

For this project, Claude Code is primarily expected to act as:

```text
Primary Developer / Implementation Agent
```

Typical responsibilities include:

- inspect the repository
- understand the approved task
- read the implementation plan when one exists
- implement the requested change
- add or update automated tests
- run relevant validation
- fix implementation errors
- report what changed
- report what was tested

Claude Code should not assume it is the final authority.

---

## 3. Role Boundaries

The long-term workflow separates responsibilities:

```text
Planner / Architect
        ↓
Developer
        ↓
Tester / Integration
        ↓
Independent Reviewer
```

A possible tool mapping is:

```text
ChatGPT / Codex → Planner / Architect
Claude Code     → Primary Developer
Cursor          → Integration / Tester / IDE
Grok            → Independent Reviewer
```

These tool assignments may change later.

The architecture depends on the **roles**, not permanently on specific vendors.

---

## 4. Stage-by-Stage Execution

This project is intentionally developed one stage at a time.

If the task says:

```text
Execute Stage 2 only.
```

then Claude Code must:

- complete Stage 2 only
- not start Stage 3
- not add speculative future infrastructure
- not build unrelated later-stage features
- stop when Stage 2 is complete
- report the result

Do not assume permission to continue to the next stage.

---

## 5. Current Development Sequence

The current roadmap is:

```text
Stage 1
Repository Foundation

Stage 2
Idea → Structured Ticket Draft

Stage 3
GitHub Duplicate Check + Human Approval + Issue Creation

Stage 4
Planner → Developer → Tester → Reviewer

Stage 5
GitHub Issue → Ready Gate → Agent Team → Pull Request

Stage 6
Advanced Automation
```

Examples of Stage 6 work include:

```text
Retry loops
Structured run history
Token / cost tracking
Git worktrees
Parallel agents
CI feedback
```

Do not implement Stage 6 capabilities early.

---

## 6. Human Approval Boundaries

Claude Code must preserve human control.

Claude Code must not independently:

- mark a GitHub Issue as `Ready`
- change roadmap priority
- merge a pull request
- deploy to production
- delete GitHub Issues
- close research tasks without evidence
- turn every idea directly into implementation
- modify production algorithms without explicit approval

A raw idea is not automatically an approved feature.

The expected boundary is:

```text
Idea
  ↓
AI Draft
  ↓
Human Approval
  ↓
GitHub Issue
  ↓
Human Ready Decision
  ↓
Coding Workflow
```

---

## 7. Repository Scope

This repository is intended to contain generic, reusable AI development workflow code and documentation.

It is not the MarketLens production repository.

Do not add:

- MarketLens production secrets
- private company code
- customer data
- private production URLs
- private credentials
- internal company-only configuration

Project-specific private integrations belong in a separate private integration layer.

---

## 8. Public Repository Safety

Assume this repository may be public.

Before creating or editing tracked files, avoid exposing:

```text
API keys
access tokens
passwords
private certificates
real customer data
private repository URLs
company-sensitive information
personal machine-specific paths
production credentials
```

Use placeholders or sample values where needed.

For example:

```text
example.com
sample-user
sample-repository
dummy-token
<PROJECT_ROOT>
```

---

## 9. Environment Files

The public template is:

```text
.env.example
```

Real local values belong in:

```text
.env
```

Never commit `.env`.

Never print secrets into:

- logs
- prompts
- GitHub Issues
- `.ai/` files
- test reports
- generated documentation

---

## 10. Coding Behaviour

When implementing work:

- keep changes focused
- avoid unrelated refactoring
- prefer small, readable modules
- avoid unnecessary dependencies
- use clear names
- preserve existing behaviour unless the task requires a change
- validate external input
- handle errors explicitly
- add tests for new behaviour
- do not change public interfaces unnecessarily

Prefer the smallest useful solution.

---

## 11. Python Guidance

When working on Python code:

- target Python 3.12+ unless project configuration says otherwise
- use type hints
- prefer Pydantic for structured AI output when appropriate
- prefer `pathlib` for filesystem paths
- keep provider-specific integrations behind small interfaces
- keep prompt templates separate from core business logic where practical
- use environment variables for secrets

Possible tools include:

```text
pytest
ruff
mypy
pydantic
python-dotenv
httpx
```

Only add dependencies required by the current stage.

---

## 12. Testing

Do not claim completion without verification.

Run the relevant checks for the current task.

Where applicable:

```text
Build
Tests
Lint
Type checking
```

Report evidence clearly.

Good:

```text
Tests: 18 passed, 0 failed
Lint: PASS
Type check: PASS
```

Not sufficient:

```text
It looks fine.
It should work.
```

Do not remove or weaken tests merely to make a task pass.

---

## 13. Git Behaviour

Follow the Git rules in `AGENTS.md`.

In particular:

- do not push directly to `main` unless explicitly instructed
- do not force-push
- do not rewrite unrelated history
- do not commit unrelated changes
- do not auto-merge
- do not commit secrets
- keep changes focused

Before claiming completion, inspect the relevant diff where practical.

---

## 14. Destructive Actions

Do not perform destructive actions without explicit human approval.

Examples:

```text
rm -rf
git reset --hard
git clean -fd
git push --force
destructive database migration
production deployment
deleting remote resources
```

If a task appears to require one of these, stop and surface it clearly.

---

## 15. `.ai/` Workflow Files

The `.ai/` folder may contain handoff artefacts such as:

```text
.ai/task.md
.ai/plan.md
.ai/implementation.md
.ai/test-report.md
.ai/review.md
```

Typical flow:

```text
task.md
  ↓
Planner
  ↓
plan.md
  ↓
Claude Code / Developer
  ↓
implementation
  ↓
Tester
  ↓
test-report.md
  ↓
Reviewer
  ↓
review.md
```

Claude Code should use these files when they are part of the current workflow.

Do not place secrets in `.ai/`.

---

## 16. Documentation Layout

Documentation is organised by purpose:

```text
docs/setup/
```

For:

```text
How do I install, configure, or build this?
```

```text
docs/explanations/
```

For:

```text
What is this concept and why does it matter?
```

```text
docs/architecture/
```

For:

```text
How is the system designed?
```

Do not put every Markdown document in the repository root.

---

## 17. Completion Report

When finishing a task, report:

```text
What changed
Files changed
Tests / validation run
Result
Any known limitations
Anything intentionally not changed
```

If the task is stage-scoped, confirm that later stages were not implemented.

Example:

```text
Stage 2 completed.

Changed:
- idea-to-ticket/...
- schemas/...
- tests/...

Validation:
- pytest: PASS
- ruff: PASS

Not implemented:
- GitHub issue creation
- duplicate detection
- multi-agent orchestration

Those belong to later stages.
```

---

## 18. Final Rules

The most important repository rule is:

> **Capturing an idea must not automatically turn that idea into code.**

The second is:

> **Complete and verify one stage before moving to the next.**

The third is:

> **Human approval remains the final authority for priority, readiness, merge, deployment, and production changes.**

The fourth is:

> **Claude Code is an implementation agent, not the repository owner or final decision-maker.**
