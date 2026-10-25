# AGENTS.md

## Project

This repository implements an AI-assisted software development workflow.

The system has two major parts:

1. **AI-Powered Idea-to-Ticket Workflow**
2. **Multi-Agent Automated Coding Workflow**

The target flow is:

```text
Idea
  ↓
AI Intake
  ↓
Structured Ticket
  ↓
Human Approval
  ↓
GitHub Issue
  ↓
Ready
  ↓
Planner / Architect
  ↓
Developer
  ↓
Tester / Integration
  ↓
Independent Reviewer
  ↓
Pull Request
  ↓
Human Review
  ↓
Merge
```

This repository is an **AI development workflow project**.

It is not the MarketLens production repository.

MarketLens or other software projects may later use this workflow as client projects.

---

# 1. Core Principles

All agents must follow these principles.

## 1.1 Human Control

AI may:

- analyse
- classify
- search
- summarise
- recommend
- draft
- implement approved work
- test
- review

AI must not independently decide:

- what should be built
- what should be prioritised as `Ready`
- whether a production algorithm should change
- whether code should be merged
- whether code should be deployed

Human approval remains the final authority.

---

## 1.2 An Idea Is Not Automatically a Feature

A raw idea may become:

```text
FEATURE
BUG
RESEARCH
EXPERIMENT
DATA
ARCHITECTURE
REFACTOR
DOCUMENTATION
```

Research or experimental ideas must not automatically become implementation tasks.

Where appropriate:

```text
Research
   ↓
Hypothesis
   ↓
Experiment
   ↓
Evidence
   ↓
Decision
   ↓
Feature
```

---

## 1.3 Stage-by-Stage Development

Do not implement multiple roadmap stages at once unless explicitly instructed.

When the task says:

```text
Execute Stage N only
```

then:

- complete only Stage N
- do not begin Stage N+1
- do not add speculative future infrastructure
- stop after the requested stage is complete

Later-stage design may be considered for compatibility, but must not be implemented prematurely.

---

## 1.4 Prefer the Smallest Useful Solution

Do not introduce unnecessary frameworks or infrastructure.

Do not add any of the following unless the current task clearly requires them:

```text
CrewAI
LangGraph
Kafka
Message queues
Kubernetes
Cloud deployment
Distributed workers
Parallel agents
Vector databases
Complex persistence layers
```

Prefer:

```text
Python
Small modules
Typed models
Simple CLI tools
GitHub CLI / API
Plain files
Clear interfaces
Automated tests
```

---

# 2. Repository Responsibilities

Recommended structure:

```text
ai-dev-workflow/
│
├── idea-to-ticket/
├── orchestrator/
├── prompts/
├── schemas/
├── tests/
├── examples/
├── docs/
│   ├── setup/
│   ├── explanations/
│   └── architecture/
├── .ai/
│
├── AGENTS.md
├── CLAUDE.md
├── README.md
├── .env.example
└── .gitignore
```

Responsibilities:

## `idea-to-ticket/`

Contains logic for:

- raw idea intake
- intent understanding
- classification
- priority suggestions
- duplicate / related issue detection
- structured ticket generation
- GitHub Issue creation after approval

## `orchestrator/`

Contains future multi-agent workflow coordination.

Do not assume this must exist in the first implementation stage.

## `prompts/`

Contains version-controlled prompt templates.

Prompts should be explicit, bounded, and testable.

## `schemas/`

Contains structured data models used by AI outputs.

Prefer typed schemas using Pydantic where appropriate.

## `tests/`

Contains automated tests.

## `.ai/`

Contains temporary or shared AI workflow artefacts, for example:

```text
task.md
plan.md
implementation.md
test-report.md
review.md
```

Do not store secrets in `.ai/`.


## `docs/setup/`

Contains practical setup and implementation guides.

Examples:

```text
fresh-setup-guide.md
```

Use this folder for documents that answer:

```text
How do I install, configure, or build this?
```

## `docs/explanations/`

Contains human-readable explanations of concepts, tools, and design principles.

Examples:

```text
what-is-agents-md.md
what-is-claude-md.md
what-is-an-orchestrator.md
structured-output-explained.md
```

Use this folder for documents that answer:

```text
What is this concept?
Why does it matter?
```

## `docs/architecture/`

Contains system design and architecture documentation.

Examples:

```text
idea-to-ticket-workflow.md
multi-agent-coding-workflow.md
public-private-repository-boundary.md
```

Use this folder for:

- component boundaries
- workflow diagrams
- Mermaid architecture diagrams
- handoff contracts
- design decisions

## `examples/`

Contains safe, generic examples for the public repository.

Examples:

```text
sample-idea.md
sample-ticket.json
sample-plan.md
sample-test-report.md
sample-review.md
```

Examples must use fake or sample data and must not contain secrets, real customer data, private repository URLs, or company-sensitive information.

## `CLAUDE.md`

Claude Code-specific entry point.

`CLAUDE.md` must not replace `AGENTS.md`.

The relationship is:

```text
AGENTS.md
→ repository-wide rules for all AI agents

CLAUDE.md
→ Claude Code-specific entry point and instructions
```

Claude Code should read and follow `AGENTS.md` before implementing work.

---

# 2.1 Public / Private Repository Boundary

Assume this repository may be public.

The public repository should contain generic, reusable workflow code and documentation such as:

```text
README.md
AGENTS.md
CLAUDE.md
docs/
examples/
prompts/
schemas/
tests/
generic orchestrator code
.env.example
```

Project-specific or sensitive integrations belong in a separate private integration layer.

Examples of private integration content:

```text
private repository adapters
internal project configuration
real project context
private test data
deployment-specific configuration
company-specific integration code
```

Secrets must not be committed to either public or private repositories.

Never commit:

```text
.env
API keys
access tokens
passwords
private certificates
production credentials
```

Use environment variables, GitHub Secrets, or an appropriate secret manager instead.

The public repository must not expose:

- private repository names or URLs
- private company information
- private production endpoints
- customer data
- credentials
- internal operational details
- personal machine-specific paths where avoidable

Use placeholders and sample values in public documentation and examples.

A useful rule is:

> If publishing a file could create security, privacy, company, or production risk, it does not belong in the public repository.

---

# 3. Idea-to-Ticket Rules

The Idea-to-Ticket system must remain separate from the coding system.

The expected boundary is:

```text
Raw Idea
   ↓
AI Triage
   ↓
Ticket Draft
   ↓
Human Approval
   ↓
GitHub Issue
   ↓
Ready?
   ↓
Coding Workflow
```

The intake workflow may:

- understand the idea
- classify the idea
- suggest ticket type
- suggest labels
- suggest priority
- search existing issues
- identify likely duplicates
- draft ticket content
- identify missing information
- recommend research before development

The intake workflow must not automatically:

- start implementation
- modify production code
- delete issues
- close research questions
- change roadmap priority
- mark an issue `Ready`
- merge code
- deploy code

---

# 4. Ticket Types

Supported ticket types should include:

```text
FEATURE
BUG
RESEARCH
EXPERIMENT
DATA
ARCHITECTURE
REFACTOR
DOCUMENTATION
```

Do not create new categories without a clear reason.

---

# 5. Priority Model

Suggested priorities:

```text
NOW
NEXT
LATER
PARKED
```

AI may recommend priority.

AI must not treat the recommendation as a final roadmap decision.

---

# 6. Structured Ticket Output

Where possible, ticket-generation logic should produce structured output.

Suggested fields:

```text
title
type
project
priority
labels
problem
hypothesis
related_context
duplicate_of
missing_information
requires_experiment
ready_for_development
recommended_action
acceptance_criteria
```

Structured output must be validated before it is used by automation.

Do not trust unvalidated LLM output.

---

# 7. Human Approval Boundary

Before any GitHub Issue is created or modified, explicit human approval is required.

If using a CLI prompt, default to:

```text
Create GitHub Issue? [y/N]
```

Default must be:

```text
No
```

No response, invalid response, or ambiguity must be treated as no approval.

---

# 8. GitHub Rules

Agents must follow these rules:

- Never delete GitHub Issues automatically.
- Never close an issue without explicit approval.
- Never move an issue to `Ready` without explicit approval.
- Never create duplicate issues intentionally.
- Search for related existing issues before creating a new issue when duplicate detection is enabled.
- Return the created issue number and URL after successful creation.
- Avoid creating duplicate issues after partial failures.

Recommended project states:

```text
Inbox
Next
Ready
In Progress
Testing
Done
Parked
```

---

# 9. Multi-Agent Coding Roles

The long-term workflow separates responsibilities.

The roles are more important than the specific AI product.

Current recommended role mapping:

```text
Harry
→ Repository owner / final authority

ChatGPT / Codex
→ Planner / Architect

Claude Code
→ Primary Developer / Implementation Agent

Cursor
→ Integration / Tester / IDE / manual inspection

Grok
→ Independent Reviewer
```

This mapping is a current implementation choice, not a permanent architectural dependency.

Agents must still think in terms of:

```text
Planner
Developer
Tester
Reviewer
```

rather than assuming one provider permanently owns a role.

## Planner / Architect

Responsible for:

- understanding requirements
- inspecting the repository
- identifying affected files
- identifying reusable code
- defining implementation steps
- identifying risks
- identifying edge cases
- defining required tests

The Planner should not modify implementation files unless explicitly instructed.

---

## Developer

Responsible for:

- reading the approved task
- reading the implementation plan
- implementing the requested change
- adding or updating tests
- running relevant tests
- fixing implementation errors
- avoiding unrelated refactoring


Current recommendation:

```text
Claude Code
→ Primary Developer
```

Claude Code should use `CLAUDE.md` as its entry point and must also follow `AGENTS.md`.

Claude Code may be used as the implementation agent during Stage 2 and Stage 3 before the formal multi-agent pipeline is complete.

In Stage 4, the Developer role becomes an explicit part of the multi-agent workflow.

---

## Tester / Integration Agent

Responsible for:

- reviewing the resulting diff
- running builds
- running unit tests
- running integration tests where applicable
- running lint
- running type checks
- detecting regressions
- reporting objective results


Current recommendation:

```text
Cursor
→ Integration / Tester / IDE
```

Cursor may also be used for:

- inspecting diffs
- interactive debugging
- integration checks
- manual fixes when explicitly required

Cursor should not be treated as the repository owner or as the only source of verification.

Testing should produce evidence.

For example:

```text
Build: PASS
Tests: 42 passed, 0 failed
Lint: PASS
Type check: PASS
```

Statements such as:

```text
"It looks fine."
"It should work."
```

are not sufficient verification.

---

## Independent Reviewer

Current recommendation:

```text
Grok
→ Independent Reviewer
```

The reviewer should remain independent from the original implementation where practical.

Responsible for reviewing:

- requirement compliance
- logic bugs
- regressions
- missing tests
- security issues
- architecture problems
- error handling
- race conditions
- unnecessary complexity
- edge cases

Where possible, the reviewer should not be the same agent that wrote the implementation.

A review should finish with a clear verdict:

```text
VERDICT: PASS
```

or:

```text
VERDICT: FAIL
```

If failed, list blocking issues clearly.

---

# 10. Creator vs Verifier Separation

Prefer separating:

```text
CREATE
Planner
Developer

VERIFY
Tester
Independent Reviewer
```

Do not let one model's opinion be the only definition of correctness.

Objective tests are more important than self-review.

---

# 11. Coding Rules

Unless a task explicitly requires otherwise:

- keep functions small
- keep modules focused
- use clear naming
- avoid unnecessary dependencies
- avoid unrelated refactoring
- do not change public interfaces without need
- prefer explicit behaviour over hidden magic
- validate external inputs
- handle failures clearly
- add tests for new behaviour
- preserve backwards-compatible behaviour where required

---

# 12. Python Rules

For Python code:

- target Python 3.12+ unless project configuration says otherwise
- use type hints
- prefer Pydantic for external structured AI output
- use `pathlib` for paths where practical
- use environment variables for secrets
- keep network integrations behind small interfaces
- make LLM providers replaceable where reasonable
- keep prompt text separate from business logic where practical

Recommended tools may include:

```text
pytest
ruff
mypy
pydantic
python-dotenv
httpx
```

Do not add all tools automatically; use only what the current stage needs.

---

# 13. Tests

Every behaviour change should have appropriate tests.

Tests should cover, where relevant:

- happy path
- invalid input
- malformed AI output
- missing required fields
- user rejects approval
- GitHub command failure
- network failure
- duplicate detection
- accidental duplicate creation
- empty idea
- unexpected ticket type
- unexpected priority

Do not remove existing tests simply to make a build pass.

---

# 14. Definition of Done

An AI agent must not define completion only by saying the task is finished.

Use objective criteria.

Where relevant:

```text
Build passes
Tests pass
Lint passes
Type checking passes
Acceptance criteria satisfied
No unrelated changes
Independent review passes
```

The exact Definition of Done should come from the current task.

---

# 15. Git Rules

Unless explicitly instructed otherwise:

- work on a feature branch
- do not push directly to `main`
- do not force-push shared branches
- do not rewrite unrelated history
- do not commit unrelated changes
- do not commit generated secrets or credentials
- keep commits focused
- show the diff before claiming completion where practical

Do not merge automatically.

---

# 16. Secrets and Security

Never commit:

```text
.env
API keys
GitHub tokens
Passwords
Private credentials
Access tokens
Private certificates
```

Commit:

```text
.env.example
```

with placeholder values only.

Do not print secrets into:

- logs
- test output
- AI prompts
- `.ai/` files
- GitHub Issues
- screenshots
- generated reports

---

# 17. External Commands

Before running destructive commands, stop and require explicit approval.

Examples include:

```text
rm -rf
git reset --hard
git clean -fd
git push --force
gh issue delete
database destructive migration
production deployment
```

Do not perform destructive actions just because they make the task easier.

---

# 18. Scope Control

Only modify files required for the current task.

Before changing unrelated architecture, ask whether it is necessary.

Avoid:

```text
"While I am here, I also refactored..."
```

Prefer:

```text
"The requested change is complete. I identified an unrelated improvement and left it unchanged."
```

---

# 19. Production Boundaries

This repository is initially an experimental development workflow.

Do not automatically:

- modify MarketLens production code
- modify DawnLuma production code
- access production databases
- deploy to production
- create financial trading actions
- change production algorithms

Any future integration with production projects must be explicitly authorised.

---

# 20. Current Development Sequence

Follow this roadmap unless the human explicitly changes it.

## Stage 1

```text
Create ai-dev-workflow repository
Set up project structure
Add README.md
Add AGENTS.md
Add CLAUDE.md
Add docs/setup/
Add docs/explanations/
Add docs/architecture/
Add examples/
Add .env.example
Add .gitignore
Document the public/private boundary
```

Claude Code is optional in Stage 1 and is not required to complete the repository foundation.

## Stage 2

```text
Raw Idea
   ↓
Structured Ticket Draft
```

No GitHub write automation yet.

Claude Code may be used as the primary implementation agent for building this MVP, but it must not implement Stage 3 functionality early.

## Stage 3

```text
Idea
 ↓
Duplicate Check
 ↓
Ticket Draft
 ↓
Human Approval
 ↓
GitHub Issue
```

Claude Code may continue as the primary implementation agent while human approval remains mandatory before GitHub write actions.

## Stage 4

```text
task.md
 ↓
Planner
 ↓
Developer
 ↓
Tester
 ↓
Reviewer
```

At this stage the role separation becomes explicit.

Current recommended mapping:

```text
ChatGPT / Codex → Planner
Claude Code     → Developer
Cursor          → Tester / Integration
Grok            → Independent Reviewer
```

## Stage 5

```text
GitHub Issue
 ↓
Ready Gate
 ↓
Agent Team
 ↓
Pull Request
```

## Stage 6

Only after the basic system works:

```text
Feedback loops
Retry limits
Logs
Token / cost tracking
Run history
Git worktrees
Parallel agents
CI feedback
```

Auto merge and auto deploy are last-stage capabilities.

---

# 21. Current Tooling Strategy

Do not hard-code the architecture around one vendor.

Current recommended working arrangement:

```text
Harry
→ Repository owner / final authority

ChatGPT / Codex
→ Planner / Architect

Claude Code
→ Primary Developer / Implementation Agent

Cursor
→ Integration / Tester / IDE

Grok
→ Independent Reviewer
```

Possible future tools may include:

```text
OpenCode
Ollama / local models
Other coding agents
Other model providers
```

The architecture should continue to think in terms of:

```text
Planner
Developer
Tester
Reviewer
```

rather than assuming one provider permanently owns a role.

`AGENTS.md` remains the vendor-neutral repository rulebook.

`CLAUDE.md` is the Claude Code-specific entry point and must defer to `AGENTS.md`.

---

# 22. Final Rule

The most important rule in this repository is:

> **Capturing an idea must not automatically turn that idea into code.**

The second most important rule is:

> **Complete and verify one stage before moving to the next.**

The third most important rule is:

> **Roles and verification boundaries matter more than AI brand names.**

The fourth most important rule is:

> **No AI agent is the repository owner. Human authority remains final.**
