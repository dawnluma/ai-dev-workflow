# AI Dev Workflow

> An AI-assisted software development workflow that manages work from **idea → ticket → implementation → review** while keeping important decisions under human control.

This repository contains the implementation and experiments for two connected workflows:

1. **AI-Powered Idea-to-Ticket Workflow**
2. **Multi-Agent Automated Coding Workflow**

The goal is not simply to use AI to write code.  
The goal is to build a controlled development system where AI can help capture, organise, implement, test, and review work without automatically turning every idea into a code change.

---

# Table of Contents

- [1. Project Overview](#1-project-overview)
- [2. Why This Project Exists](#2-why-this-project-exists)
- [3. Overall Architecture](#3-overall-architecture)
- [4. Public and Private Repository Boundary](#4-public-and-private-repository-boundary)
- [5. Idea-to-Ticket Workflow](#5-idea-to-ticket-workflow)
- [6. Multi-Agent Coding Workflow](#6-multi-agent-coding-workflow)
- [7. Human Approval Boundaries](#7-human-approval-boundaries)
- [8. Repository Structure](#8-repository-structure)
- [9. Folder Explanations](#9-folder-explanations)
  - [9.1 `idea-to-ticket/`](#91-idea-to-ticket)
  - [9.2 `orchestrator/`](#92-orchestrator)
  - [9.3 `prompts/`](#93-prompts)
  - [9.4 `schemas/`](#94-schemas)
  - [9.5 `tests/`](#95-tests)
  - [9.6 `.ai/`](#96-ai)
  - [9.7 `docs/setup/`](#97-docssetup)
  - [9.8 `docs/explanations/`](#98-docsexplanations)
  - [9.9 `docs/architecture/`](#99-docsarchitecture)
  - [9.10 `examples/`](#910-examples)
- [10. Important Files](#10-important-files)
- [11. Current Development Roadmap](#11-current-development-roadmap)
- [12. Stage 1 — Repository Foundation](#12-stage-1--repository-foundation)
- [13. Stage 2 — Idea-to-Ticket MVP](#13-stage-2--idea-to-ticket-mvp)
- [14. Stage 3 — GitHub Integration](#14-stage-3--github-integration)
- [15. Stage 4 — Multi-Agent Coding MVP](#15-stage-4--multi-agent-coding-mvp)
- [16. Stage 5 — Connect the Two Workflows](#16-stage-5--connect-the-two-workflows)
- [17. Stage 6 — Advanced Automation](#17-stage-6--advanced-automation)
- [18. Tooling Strategy](#18-tooling-strategy)
- [19. Setup](#19-setup)
- [20. Basic Usage](#20-basic-usage)
- [21. Ticket Types](#21-ticket-types)
- [22. Priority Model](#22-priority-model)
- [23. Structured Ticket Model](#23-structured-ticket-model)
- [24. GitHub Project States](#24-github-project-states)
- [25. Testing and Definition of Done](#25-testing-and-definition-of-done)
- [26. Security and Secrets](#26-security-and-secrets)
- [27. Relationship with `AGENTS.md`](#27-relationship-with-agentsmd)
- [28. Documentation Layout](#28-documentation-layout)
- [29. Example End-to-End Flow](#29-example-end-to-end-flow)
- [30. Long-Term Goal](#30-long-term-goal)
- [31. Core Principles](#31-core-principles)

---

# 1. Project Overview

This repository is an experimental AI development workflow project.

It is intended to be publishable as a **public repository** containing generic framework code, documentation, schemas, tests, examples, and architecture. Sensitive project integrations and private operational context belong in a separate private integration layer.

It is designed to answer two questions:

1. Can AI help turn a rough idea into a useful, structured GitHub Issue before coding starts?
2. Once an issue is approved and ready, can multiple AI agents collaborate through planning, implementation, testing, and review?

The intended end-to-end flow is:

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

This repository is **not** intended to contain MarketLens production code.

A project such as MarketLens may later become a real consumer of this workflow.

---

# 2. Why This Project Exists

The main problem is not a lack of ideas.

The problem is that new ideas can interrupt the work already in progress.

Without a controlled workflow:

```text
New idea
  ↓
Stop current work
  ↓
Start coding the new idea
  ↓
Another idea appears
  ↓
Change direction again
```

The target behaviour is:

```text
New idea
  ↓
Capture it
  ↓
AI organises it
  ↓
Human reviews it
  ↓
Backlog remembers it
  ↓
Current work continues
```

The second problem is repetitive manual coordination between AI tools.

Instead of repeatedly copying requirements between different tools, the long-term goal is:

```text
Approved GitHub Issue
  ↓
Orchestrator
  ↓
Planner
  ↓
Developer
  ↓
Tester
  ↓
Reviewer
  ↓
Pull Request
```

---

# 3. Overall Architecture

```mermaid
flowchart TD
    A[Harry / Human] --> B[Raw Idea]
    B --> C[AI Idea Intake]
    C --> D[Classify / Triage]
    D --> E[Duplicate / Related Issue Check]
    E --> F[Priority Suggestion]
    F --> G[Structured Ticket Draft]
    G --> H{Human Approval}

    H -->|Reject| I[Reject]
    H -->|Park| J[Parked]
    H -->|Approve| K[GitHub Issue]

    K --> L{Ready for Development?}
    L -->|No| M[Backlog / Next / Research]
    L -->|Yes| N[AI Orchestrator]

    N --> O[Planner / Architect]
    O --> P[Developer]
    P --> Q[Tester / Integration]
    Q --> R[Independent Reviewer]

    R -->|FAIL| P
    R -->|PASS| S[Pull Request]

    S --> T{Human Review}
    T -->|Changes Requested| P
    T -->|Approved| U[Merge]
```

The most important boundary is:

```text
GitHub Issue
```

The **Idea-to-Ticket workflow** finishes when an approved issue is created and properly classified.

The **Multi-Agent Coding workflow** begins only when the issue is explicitly considered ready for implementation.

---

# 4. Public and Private Repository Boundary

This repository is intended to be safe to publish as a **public framework, learning project, and portfolio repository**.

The public repository should contain only generic and reusable material:

```text
PUBLIC
ai-dev-workflow
│
├── README.md
├── AGENTS.md
├── CLAUDE.md
├── docs/
├── architecture diagrams
├── generic schemas
├── generic tests
├── examples/
├── prompts/
└── generic orchestrator code
```

Project-specific or sensitive integrations belong outside this public repository.

```text
PRIVATE INTEGRATION LAYER
│
├── private repository adapters
├── internal configuration
├── real project context
├── private test data
└── deployment-specific configuration
```

Secrets should not be committed to either public or private repositories.

```text
Never commit:
.env
API keys
access tokens
passwords
private certificates
production credentials
```

Use environment variables, GitHub Secrets, or an appropriate secret manager instead.

The intended relationship is:

```mermaid
flowchart TD
    A[Public: ai-dev-workflow]
    A --> B[Generic Interfaces / Contracts]
    A --> C[Generic Orchestrator]
    A --> D[Generic Schemas]
    A --> E[Docs / Tests / Examples]

    B --> F[Private Integration Layer]
    C --> F
    D --> F

    F --> G[Real Project such as MarketLens]
```

The public README should document this boundary at a high level, but it should **not** expose:

- private repository names or URLs
- private company information
- private production endpoints
- real credentials
- customer data
- internal operational details

A useful rule is:

> If publishing a file could create security, privacy, company, or production risk, it does not belong in the public repository.

---

# 5. Idea-to-Ticket Workflow

The first workflow focuses on everything that happens **before coding starts**.

```mermaid
flowchart TD
    A[Raw Idea] --> B[Understand Intent]
    B --> C[Classify]
    C --> D[Search Existing Issues]
    D --> E{Possible Duplicate?}

    E -->|Yes| F[Recommend Update / Review Existing Issue]
    E -->|No| G[Create New Ticket Draft]

    F --> H[Priority Suggestion]
    G --> H

    H --> I[Structured Output]
    I --> J{Human Approval}

    J -->|No| K[Stop / Park / Edit]
    J -->|Yes| L[Create GitHub Issue]
```

The AI may help with:

- understanding the idea
- classification
- identifying missing information
- suggesting priority
- searching for related tickets
- suggesting duplicates
- drafting ticket content
- suggesting labels
- identifying whether research is required
- generating structured output

The AI must not automatically:

- start implementation
- change production code
- change production algorithms
- mark work as `Ready`
- merge code
- deploy code

---

# 6. Multi-Agent Coding Workflow

Once an issue is approved and marked ready, the coding workflow begins.

```mermaid
flowchart TD
    A[Approved Ready Issue] --> B[Orchestrator]
    B --> C[Planner / Architect]
    C --> D[plan.md]
    D --> E[Developer]
    E --> F[Implementation + Tests]
    F --> G[Tester / Integration]
    G --> H[test-report.md]
    H --> I[Independent Reviewer]
    I --> J{Verdict}

    J -->|FAIL| K[Blocking Issues]
    K --> E

    J -->|PASS| L[Pull Request]
    L --> M[Human Review]
```

Initial role model:

```text
Planner / Architect
        ↓
Developer
        ↓
Tester / Integration
        ↓
Independent Reviewer
```

Current recommended role mapping:

```text
Harry           → Repository owner / final authority
ChatGPT / Codex → Planner / Architect
Claude Code     → Primary Developer / Implementation Agent
Cursor          → Integration / Tester / IDE
Grok            → Independent Reviewer
```

Claude Code can be used as the primary implementation agent before the formal multi-agent pipeline is complete.

However, the architecture should still depend on **roles**, not permanently on specific vendors.

---

# 7. Human Approval Boundaries

Human control is a core design principle.

```mermaid
flowchart LR
    A[AI Can Recommend] --> B[Human Decides]
    B --> C[Action]
```

AI may:

```text
Analyse
Classify
Search
Summarise
Draft
Recommend
Implement approved work
Test
Review
```

The human remains responsible for:

```text
Approve
Reject
Park
Change priority
Mark Ready
Authorise development
Approve merge
Authorise deployment
```

A useful boundary is:

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

# 8. Repository Structure

Recommended public repository structure:

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
├── .gitignore
└── .env.example
```

Mermaid view:

```mermaid
flowchart TD
    A[ai-dev-workflow/] --> B[idea-to-ticket/]
    A --> C[orchestrator/]
    A --> D[prompts/]
    A --> E[schemas/]
    A --> F[tests/]
    A --> G[examples/]
    A --> H[docs/]
    H --> H1[setup/]
    H --> H2[explanations/]
    H --> H3[architecture/]
    A --> I[.ai/]
    A --> J[AGENTS.md]
    A --> K[CLAUDE.md]
    A --> L[README.md]
    A --> M[.gitignore]
    A --> N[.env.example]
```

---

# 9. Folder Explanations

## 9.1 `idea-to-ticket/`

Purpose:

> Contains the application logic for converting a raw idea into a structured ticket.

Possible responsibilities:

```text
Input handling
Idea analysis
Classification
Priority recommendation
Duplicate checking
Ticket drafting
GitHub issue creation
Human approval flow
```

Example future files:

```text
idea-to-ticket/
├── cli.py
├── intake.py
├── classifier.py
├── duplicate_checker.py
├── ticket_builder.py
└── github_client.py
```

This should be the first real application area developed in the project.

---

## 9.2 `orchestrator/`

Purpose:

> Coordinates the multi-agent coding workflow after an issue is ready for development.

Possible responsibilities:

```text
Read task
Invoke planner
Pass plan to developer
Run tester
Invoke reviewer
Handle PASS / FAIL
Control retry loops
Track execution state
```

Example future files:

```text
orchestrator/
├── runner.py
├── agents.py
├── workflow.py
├── retry.py
└── state.py
```

This folder should remain minimal until the Idea-to-Ticket workflow is stable.

---

## 9.3 `prompts/`

Purpose:

> Stores version-controlled prompts used by AI agents.

Instead of hiding important prompts inside Python strings, prompts can be stored separately.

Example:

```text
prompts/
├── idea_intake.md
├── ticket_classifier.md
├── planner.md
├── developer.md
├── tester.md
└── reviewer.md
```

Benefits:

- easier review
- easier version control
- easier prompt testing
- easier provider replacement
- easier comparison between prompt versions

---

## 9.4 `schemas/`

Purpose:

> Defines structured data models used between different parts of the workflow.

Example models:

```text
TicketDraft
IssueSearchResult
PlannerOutput
TestReport
ReviewResult
WorkflowRun
```

Possible structure:

```text
schemas/
├── ticket.py
├── planning.py
├── testing.py
└── review.py
```

Pydantic may be used where appropriate.

The goal is to avoid passing unreliable free-form text between automated stages.

---

## 9.5 `tests/`

Purpose:

> Contains automated tests for the workflow.

Example:

```text
tests/
├── test_intake.py
├── test_classifier.py
├── test_ticket_schema.py
├── test_duplicate_checker.py
├── test_github_client.py
└── test_orchestrator.py
```

Tests should verify behaviour rather than AI opinions.

Examples:

```text
Valid ticket output
Malformed model output
Missing fields
User rejects approval
GitHub command failure
Duplicate issue detection
Retry limit behaviour
```

---

## 9.6 `.ai/`

Purpose:

> Stores temporary or shared artefacts generated during an AI coding workflow run.

Example:

```text
.ai/
├── task.md
├── plan.md
├── implementation.md
├── test-report.md
└── review.md
```

Typical flow:

```mermaid
flowchart TD
    A[task.md] --> B[Planner]
    B --> C[plan.md]
    C --> D[Developer]
    D --> E[implementation.md]
    E --> F[Tester]
    F --> G[test-report.md]
    G --> H[Reviewer]
    H --> I[review.md]
```

These files make agent handoffs visible and auditable.

Do not store secrets in `.ai/`.

---

## 9.7 `docs/setup/`

Purpose:

> Stores practical setup and implementation guides.

Example:

```text
docs/setup/
└── fresh-setup-guide.md
```

Use this folder for documents that answer:

```text
How do I install, configure, or build this?
```

---

## 9.8 `docs/explanations/`

Purpose:

> Stores human-readable Markdown documents that explain concepts, tools, and design principles.

Example:

```text
docs/explanations/
├── what-is-agents-md.md
├── what-is-claude-md.md
├── what-is-an-orchestrator.md
├── structured-output-explained.md
├── human-approval-boundaries.md
└── local-vs-cloud-models.md
```

Use this folder for documents that answer:

```text
What is this concept?
Why does it matter?
```

---

## 9.9 `docs/architecture/`

Purpose:

> Stores system design and architecture documentation.

Example:

```text
docs/architecture/
├── idea-to-ticket-workflow.md
├── multi-agent-coding-workflow.md
└── public-private-repository-boundary.md
```

This folder is appropriate for:

- component boundaries
- workflow handoffs
- Mermaid architecture diagrams
- design decisions

---

## 9.10 `examples/`

Purpose:

> Provides safe, generic examples that demonstrate how the workflow works without exposing real project data.

Example:

```text
examples/
├── sample-idea.md
├── sample-ticket.json
├── sample-plan.md
├── sample-test-report.md
└── sample-review.md
```

All examples should use fake or sample data.

Avoid real:

```text
emails
customer IDs
access tokens
private repository URLs
production payloads
company-sensitive data
```

---

# 10. Important Files

## `README.md`

Purpose:

> The front door of the project.

It explains:

- what the project is
- why it exists
- how it works
- repository structure
- setup
- usage
- roadmap

---

## `AGENTS.md`

Purpose:

> Project-wide operating rules for AI coding agents.

It defines:

- coding rules
- testing rules
- Git rules
- security rules
- human approval boundaries
- stage boundaries
- agent responsibilities

Simple distinction:

```text
README.md
= What this project is and how to use it

AGENTS.md
= How AI agents must behave while working in it
```

---

## `CLAUDE.md`

Purpose:

> Claude Code-specific entry point for this repository.

`CLAUDE.md` does not replace `AGENTS.md`.

The intended relationship is:

```text
AGENTS.md
= Vendor-neutral rules for all AI agents

CLAUDE.md
= Claude Code-specific entry point
```

Claude Code should read and follow:

```text
AGENTS.md
README.md
```

and, when a workflow task exists:

```text
.ai/task.md
```

`CLAUDE.md` may define Claude-specific behaviour, but repository-wide rules remain in `AGENTS.md`.

---

## `.env.example`

Purpose:

> Documents required environment variables without containing real secrets.

Example:

```text
OPENAI_API_KEY=
GITHUB_TOKEN=
```

Real secrets belong in:

```text
.env
```

and `.env` must not be committed.

---

# 11. Current Development Roadmap

```mermaid
flowchart TD
    A[Stage 1<br/>Repository Foundation]
    --> B[Stage 2<br/>Idea-to-Ticket MVP]
    --> C[Stage 3<br/>GitHub Integration]
    --> D[Idea-to-Ticket Complete]
    --> E[Stage 4<br/>Multi-Agent Coding MVP]
    --> F[Stage 5<br/>Connect Both Workflows]
    --> G[Full Basic Pipeline]
    --> H[Stage 6<br/>Advanced Automation]
```

Rule:

> Complete and verify one stage before moving to the next.

---

# 12. Stage 1 — Repository Foundation

Goal:

```text
Create a clean project foundation.
```

Tasks:

```text
Create GitHub repository
Clone locally
Create folders
Add README.md
Add AGENTS.md
Add CLAUDE.md
Add docs/setup/
Add docs/explanations/
Add docs/architecture/
Add examples/
Add .gitignore
Add .env.example
Document the public/private boundary
Create first commit
Push baseline
```

Do not build the agent workflow yet.

Claude Code is optional in Stage 1 and is not required to complete the repository foundation.

Definition of Done:

```text
Repository exists
Git works
Main branch is clean
Project structure exists
Public/private boundary is documented
No secrets or private project data are committed
Baseline commit pushed
```

---

# 13. Stage 2 — Idea-to-Ticket MVP

Goal:

```text
Raw Idea
  ↓
Structured Ticket Draft
```

Example CLI:

```bash
python idea.py
```

Prompt:

```text
What is your idea?
```

Example input:

```text
I want to investigate market breadth for MarketLens.
```

Possible output:

```text
Title:
Research Market Regime Detection

Type:
RESEARCH

Priority:
NEXT

Problem:
...

Hypothesis:
...

Ready for Development:
No

Recommended Action:
Create research ticket
```

No GitHub write action yet.

Claude Code may be used as the **primary implementation agent** for building the Stage 2 MVP.

At this stage Claude Code must not implement Stage 3 functionality early.

---

# 14. Stage 3 — GitHub Integration

Goal:

```mermaid
flowchart TD
    A[Idea] --> B[AI Draft]
    B --> C[Search GitHub Issues]
    C --> D{Related / Duplicate?}
    D -->|Yes| E[Recommend Existing Issue]
    D -->|No| F[New Ticket Draft]
    E --> G[Human Review]
    F --> G
    G --> H{Approve?}
    H -->|No| I[Stop]
    H -->|Yes| J[Create GitHub Issue]
```

Suggested approval prompt:

```text
Create GitHub Issue? [y/N]
```

Default:

```text
No
```

No approval means no GitHub write.

Claude Code may continue as the primary implementation agent for Stage 3, but human approval remains mandatory before creating or modifying GitHub Issues.

---

# 15. Stage 4 — Multi-Agent Coding MVP

Goal:

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

First version should be sequential.

Do not start with parallel agents.

At Stage 4, role separation becomes an explicit part of the workflow.

Current recommended mapping:

```text
ChatGPT / Codex → Planner / Architect
Claude Code     → Primary Developer
Cursor          → Integration / Tester / IDE
Grok            → Independent Reviewer
```

The specific brands may change later. The workflow should continue to depend on roles and verification boundaries rather than one vendor.

---

# 16. Stage 5 — Connect the Two Workflows

Goal:

```mermaid
flowchart TD
    A[Idea] --> B[AI Intake]
    B --> C[GitHub Issue]
    C --> D{Ready?}
    D -->|No| E[Backlog]
    D -->|Yes| F[Orchestrator]
    F --> G[Agent Team]
    G --> H[Pull Request]
```

This creates the first complete end-to-end system.

Important rule:

> Creating an issue and starting implementation are two different decisions.

---

# 17. Stage 6 — Advanced Automation

Only after the basic workflow is stable.

Possible additions:

```text
Retry limits
Error handling
Structured JSON
Logging
Token tracking
Cost tracking
Run history
Git branches
Git worktrees
Parallel agents
CI feedback
Automated PR creation
```

Feedback loop:

```mermaid
flowchart TD
    A[Reviewer] --> B{PASS / FAIL}
    B -->|PASS| C[Pull Request]
    B -->|FAIL| D[Developer Fix]
    D --> E[Tester Retest]
    E --> A
```

Only at the final stage consider:

```text
Auto merge
Auto deploy
Production changes
```

---

# 18. Tooling Strategy

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

Claude Code may start being useful from **Stage 2**, when implementation begins.

At **Stage 4**, Claude Code formally occupies the Developer role in the multi-agent workflow.

Cursor remains valuable as:

```text
IDE
Diff inspection
Integration testing
Debugging
Manual verification
Targeted fixes when needed
```

Possible future tools:

```text
OpenCode
Ollama
Local models
Other coding agents
Other model providers
```

Important:

> The architecture should depend on roles, not vendor names.

Think in terms of:

```text
Planner
Developer
Tester
Reviewer
```

rather than:

```text
Tool A
Tool B
Tool C
Tool D
```

`AGENTS.md` remains the vendor-neutral rulebook.

`CLAUDE.md` is the Claude Code-specific entry point.

---

# 19. Setup

Initial recommended environment:

```text
Python 3.12+
Git
GitHub CLI
VS Code or Cursor
```

Check versions:

```bash
python --version
git --version
gh --version
```

Recommended when implementation begins:

```text
Claude Code
```

Optional later:

```text
OpenCode
Ollama
```

Python environment example:

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Linux / WSL:

```bash
source .venv/bin/activate
```

Install dependencies later when `requirements.txt` or `pyproject.toml` is added.

---

# 20. Basic Usage

The first meaningful CLI may eventually look like:

```bash
python idea.py
```

or:

```bash
python -m idea_to_ticket
```

Example:

```text
What is your idea?

> Investigate whether market breadth and the S&P 500 trend
  can improve MarketLens signal quality.
```

Expected result:

```text
Type:
RESEARCH / EXPERIMENT

Priority:
NEXT

Development Ready:
No

Recommended Action:
Create research ticket

Required Next Step:
Define baseline and backtest metrics
```

---

# 21. Ticket Types

Initial supported types:

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

Do not automatically treat research as implementation work.

Example:

```text
Research
  ↓
Hypothesis
  ↓
Experiment
  ↓
Backtest / Evidence
  ↓
Decision
  ↓
Feature
```

---

# 22. Priority Model

Suggested priorities:

```text
NOW
NEXT
LATER
PARKED
```

AI may recommend priority.

The human decides final roadmap priority.

---

# 23. Structured Ticket Model

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

Example:

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
  "requires_experiment": true,
  "ready_for_development": false,
  "recommended_action": "Create research ticket"
}
```

Structured output should be validated before being used by automation.

---

# 24. GitHub Project States

Suggested work queue:

```mermaid
flowchart LR
    A[Inbox] --> B[Next]
    B --> C[Ready]
    C --> D[In Progress]
    D --> E[Testing]
    E --> F[Done]

    A --> G[Parked]
    B --> G
```

Meaning:

```text
Inbox
= Captured but not prioritised

Next
= Worth doing soon

Ready
= Approved and ready for implementation

In Progress
= Actively being worked on

Testing
= Implementation complete, validation in progress

Done
= Finished

Parked
= Keep the idea, but do not work on it now
```

---

# 25. Testing and Definition of Done

AI should not define completion only by saying:

```text
Done.
```

Use objective evidence.

Possible Definition of Done:

```text
Build passes
Tests pass
Lint passes
Type checking passes
Acceptance criteria satisfied
No unrelated changes
Independent review passes
```

Example test report:

```text
Build: PASS
Tests: 42 passed, 0 failed
Lint: PASS
Type check: PASS
```

---

# 26. Security and Secrets

Never commit:

```text
.env
API keys
GitHub tokens
Passwords
Private credentials
Access tokens
Certificates
```

Commit:

```text
.env.example
```

with placeholders only.

Do not print secrets into:

```text
Logs
Prompts
GitHub Issues
.ai files
Test reports
Screenshots
```

---

# 27. Relationship with `AGENTS.md`

`AGENTS.md` contains the operating rules for AI agents.

Simple model:

```text
README.md
  ↓
What is this project?

AGENTS.md
  ↓
How must all AI agents behave?

CLAUDE.md
  ↓
How should Claude Code enter and work in this repository?

.ai/task.md
  ↓
What are we doing now?

.ai/plan.md
  ↓
How will we do it?

.ai/test-report.md
  ↓
Did it work?

.ai/review.md
  ↓
Is it acceptable?
```

Agents should read `AGENTS.md` before working in the repository.

Claude Code should additionally use `CLAUDE.md` as its tool-specific entry point.

---

# 28. Documentation Layout

Documentation is grouped by purpose:

```text
docs/
├── setup/
├── explanations/
└── architecture/
```

Use:

```text
docs/setup/
= How do I install, configure, or build this?

docs/explanations/
= What is this concept and why does it matter?

docs/architecture/
= How is the system designed?
```

Examples:

```text
docs/setup/fresh-setup-guide.md

docs/explanations/what-is-agents-md.md
docs/explanations/what-is-claude-md.md
docs/explanations/what-is-an-orchestrator.md
docs/explanations/idea-to-ticket-overview.md
docs/explanations/structured-output-explained.md
docs/explanations/human-approval-boundaries.md
docs/explanations/local-vs-cloud-models.md
docs/explanations/github-project-states.md

docs/architecture/idea-to-ticket-workflow.md
docs/architecture/multi-agent-coding-workflow.md
docs/architecture/public-private-repository-boundary.md
```

The README should remain the project front door rather than becoming the home for every detailed explanation.

---

# 29. Example End-to-End Flow

Example idea:

```text
I want to investigate using market breadth and
the S&P 500 trend to improve the scanner.
```

```mermaid
flowchart TD
    A[Raw Idea] --> B[AI Intake]
    B --> C[Classify as Research / Experiment]
    C --> D[Priority Suggestion: NEXT]
    D --> E[Development Ready: NO]
    E --> F[Create Research Ticket Draft]
    F --> G[Human Approval]
    G --> H[GitHub Issue]
    H --> I[Research / Backtest]
    I --> J{Evidence Supports Change?}
    J -->|No| K[Close / Park Research]
    J -->|Yes| L[Create Feature / Implementation Issue]
    L --> M[Mark Ready]
    M --> N[Multi-Agent Coding Workflow]
    N --> O[Pull Request]
    O --> P[Human Review]
```

This prevents an untested idea from immediately changing production behaviour.

---

# 30. Long-Term Goal

The complete target system is:

```mermaid
flowchart TD
    A[Ideas / Bugs / Research / Feedback]
    --> B[AI Idea-to-Ticket Workflow]
    --> C[Prioritised GitHub Issues]
    --> D[Ready Gate]
    --> E[Automated Multi-Agent Coding Workflow]
    --> F[Pull Request]
    --> G[Human Review]
    --> H[Merge]
```

Eventually the workflow may support:

```text
Multiple repositories
Multiple AI providers
Different agent role assignments
Prompt versioning
Evaluation datasets
Cost tracking
Run history
CI feedback loops
Local models
Cloud models
```

But these should be added gradually.

---

# 31. Core Principles

## Principle 1

> **Capturing an idea must not automatically turn that idea into code.**

---

## Principle 2

> **Complete and verify one stage before moving to the next.**

---

## Principle 3

> **Roles and verification boundaries matter more than AI brand names.**

---

## Principle 4

> **Objective tests are more important than AI confidence.**

---

## Principle 5

> **Human approval remains the final authority for priority, readiness, merge, deployment, and production changes.**

---

## Principle 6

> **No AI agent is the repository owner. Human authority remains final.**

---

## Current Starting Point

The current starting point is:

```text
Stage 1
  ↓
Create the ai-dev-workflow repository
  ↓
Create the base folder structure
  ↓
Add README.md
  ↓
Add AGENTS.md
  ↓
Add CLAUDE.md
  ↓
Add docs/setup/
  ↓
Add docs/explanations/
  ↓
Add docs/architecture/
  ↓
Add examples/
  ↓
Document the public/private boundary
  ↓
Create the first baseline commit
```

After Stage 1 is verified, move to:

```text
Stage 2
  ↓
Build the Idea-to-Ticket MVP
```
