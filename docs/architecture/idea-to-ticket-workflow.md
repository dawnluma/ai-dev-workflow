# Idea-to-Ticket Workflow Architecture

## 1. Purpose

The Idea-to-Ticket workflow converts unstructured product ideas, design notes, requirements, or specifications into structured, reviewable work items.

The workflow must support both:

1. **Small inputs** that can reasonably become a single ticket.
2. **Large inputs** that need to be decomposed into a hierarchy of work items such as Epics, Stories, Tasks, Research items, Architecture items, Data work, Documentation, Bugs, or Refactors.

The workflow is designed to improve consistency, traceability, and planning quality while preserving human control.

The system must never assume that capturing or decomposing an idea automatically authorizes implementation.

---

# 2. Core Principle

The most important rule is:

```text
AI analysis
≠ approval
≠ Ready status
≠ permission to start coding
```

The AI may:

- understand an idea,
- classify the work,
- recommend a work-item type,
- suggest priority,
- identify missing information,
- propose acceptance criteria,
- propose a work breakdown,
- recommend whether work appears ready.

The AI must not:

- approve its own recommendation,
- mark work as Ready without human approval,
- start implementation automatically,
- create or modify production systems,
- bypass the repository owner.

The human repository owner remains the final authority.

---

# 3. Supported Input Types

The workflow should not assume that all inputs are short ideas.

Supported inputs may include:

```text
Small idea
Feature request
Bug description
Research question
Experiment proposal
Architecture note
Product requirement
Large design document
Technical specification
Project proposal
System design
Roadmap notes
```

Examples:

```text
"I want the workflow to detect duplicate ideas."
```

or:

```text
A complete MarketLens product design containing:
- market data ingestion
- screening
- VCP detection
- market regime analysis
- watchlists
- backtesting
- alerts
- authentication
- UI
```

The workflow must decide whether the input is best represented as:

```text
Single Ticket
```

or:

```text
Hierarchical Work Breakdown
```

---

# 4. High-Level Flow

```text
                    HUMAN INPUT
                        │
                        ▼
          Idea / Design / Specification
                        │
                        ▼
                 AI Intake Analysis
                        │
                        ▼
              Determine Input Scope
                        │
                ┌───────┴────────┐
                │                │
                ▼                ▼
          Small / Focused     Large / Complex
                │                │
                ▼                ▼
          Single Ticket     Work Breakdown
                                 │
                                 ▼
                               Epic
                          ┌──────┴──────┐
                          ▼             ▼
                       Story          Story
                     ┌───┴───┐         │
                     ▼       ▼         ▼
                   Task    Task      Research

                        │
                        ▼
                   Human Review
                        │
               Approve / Edit / Park
                        │
                        ▼
                 GitHub Integration
                   (Stage 3+ only)
```

---

# 5. Small Input Path

A small or focused idea should normally produce a single ticket draft.

Example:

```text
Input:
"I want the workflow to detect duplicate ideas before new work is started."
```

Possible output:

```text
Title:
Add Duplicate Idea Detection

Type:
FEATURE

Priority:
NEXT

Problem:
New ideas may duplicate existing work.

Hypothesis:
Checking existing work before development may reduce duplicated effort.

Ready for Development:
No

Recommended Action:
Review duplicate detection design before implementation.
```

The AI should not decompose a simple idea into unnecessary sub-tasks.

The goal is to keep small work small.

---

# 6. Large Input Path

A large design, specification, or requirements document may contain multiple capabilities and should not be forced into a single oversized ticket.

Instead, the workflow should propose a structured work breakdown.

Example:

```text
MarketLens Product Design
        │
        ▼
AI Analysis
        │
        ▼
Work Breakdown
        │
        ├── Epic: Market Data Platform
        │   ├── Story: Historical market data ingestion
        │   │   ├── Task: Define market data provider interface
        │   │   ├── Task: Implement EOD ingestion
        │   │   └── Task: Add ingestion tests
        │   │
        │   └── Story: Local market data cache
        │       ├── Task: Define cache layout
        │       └── Task: Implement Parquet storage
        │
        ├── Epic: Stock Screening
        │   └── Story: VCP pattern detection
        │       ├── Research: Confirm VCP rules
        │       ├── Task: Implement contraction detection
        │       └── Task: Add historical validation
        │
        └── Epic: Market Regime
            ├── Research: Evaluate regime indicators
            ├── Story: Market regime classification
            └── Task: Implement regime service
```

This breakdown is a proposal only.

It does not automatically create GitHub Issues or authorize development.

---

# 7. Work-Item Hierarchy

The workflow should support a flexible hierarchy rather than assuming every item must be an Epic, Story, or Task.

## 7.1 Epic

An Epic represents a large capability, business outcome, or major system area.

Examples:

```text
Market Data Platform
Stock Screening
Market Regime Analysis
Backtesting
Authentication
```

An Epic is usually too large to implement directly.

It should normally contain multiple child work items.

## 7.2 Story

A Story represents a meaningful user-facing or system-facing capability that delivers a clear outcome.

Examples:

```text
Detect VCP patterns
Import historical market data
Create a user watchlist
Classify overall market regime
```

A Story should be small enough to reason about and validate, but it may still require several implementation tasks.

## 7.3 Task

A Task represents a concrete piece of implementation work.

Examples:

```text
Implement contraction detection
Create provider interface
Add schema validation
Write ingestion unit tests
Add Parquet cache loader
```

A Task should be specific enough that a developer or implementation agent can understand what must be done.

---

# 8. Additional Work-Item Types

Not every child item should be forced into the Story or Task categories.

The workflow should also support:

```text
RESEARCH
EXPERIMENT
ARCHITECTURE
DATA
BUG
REFACTOR
DOCUMENTATION
```

Examples:

```text
RESEARCH
Evaluate market breadth indicators

EXPERIMENT
Compare two VCP detection algorithms

ARCHITECTURE
Define market data provider abstraction

DATA
Backfill five years of historical prices

BUG
Fix incorrect scanner ranking

REFACTOR
Extract cache logic into a separate service

DOCUMENTATION
Document market regime calculation
```

This keeps the work breakdown semantically useful instead of forcing all work into one category.

---

# 9. Parent / Child Relationships

Every decomposed work item should be able to identify its parent.

Conceptually:

```text
Epic
 └── Story
      ├── Task
      ├── Task
      └── Research
```

A work item may include fields such as:

```text
id
parent_id
level
type
title
```

Example:

```json
{
  "id": "story-vcp-detection",
  "parent_id": "epic-stock-screening",
  "level": "story",
  "type": "feature",
  "title": "Detect VCP patterns"
}
```

The exact schema may evolve during implementation, but parent-child relationships must remain explicit.

---

# 10. Source Traceability

Large-input decomposition must preserve traceability back to the original design or source material.

Each proposed work item should be able to record where it came from.

Recommended concept:

```text
source_context
```

Example:

```json
{
  "title": "Implement VCP detector",
  "type": "feature",
  "parent_id": "story-vcp-detection",
  "source_context": [
    "MarketLens Design > Stock Screening > VCP Detection",
    "MarketLens Design > Detection Rules"
  ]
}
```

This allows a reviewer to answer:

```text
Why did the AI create this work item?
```

with:

```text
Because it was derived from:
MarketLens Design
→ Stock Screening
→ VCP Detection
```

Traceability is important for:

- reviewing AI decomposition,
- avoiding invented requirements,
- understanding design intent,
- revisiting source decisions,
- auditing future automated workflows.

---

# 11. Proposed Ticket Fields

A single ticket may contain fields such as:

```text
title
type
project
priority
labels
problem
hypothesis
related_context
source_context
parent_id
duplicate_of
missing_information
requires_experiment
ready_for_development
recommended_action
acceptance_criteria
```

For decomposed work, additional hierarchy-related fields may later be introduced, for example:

```text
work_item_level
children
dependencies
source_context
```

The exact Stage 2C schema should be defined before implementation.

---

# 12. Scope Detection

The AI should evaluate whether the supplied input is appropriately handled as one ticket or requires decomposition.

Conceptually:

```text
Input
  ↓
Is this focused enough to be one independently understandable work item?
  │
  ├── Yes
  │    ↓
  │  Single Ticket Draft
  │
  └── No
       ↓
     Decompose
       ↓
     Proposed Work Breakdown
```

Signals that decomposition may be required include:

- multiple independent capabilities,
- multiple user outcomes,
- several system components,
- architecture plus implementation plus research,
- many acceptance criteria covering unrelated areas,
- work that would clearly require multiple implementation phases.

The AI should avoid unnecessary decomposition of small ideas.

---

# 13. Human Review Gate

Every AI-generated ticket or work breakdown must be treated as a draft.

The human may:

```text
Approve
Edit
Merge items
Split items
Change hierarchy
Change priority
Change type
Park
Reject
Request more analysis
```

The AI may recommend:

```text
priority = NEXT
ready_for_development = true
```

but these values do not become authoritative until reviewed.

---

# 14. Ready Gate

The workflow must preserve an explicit Ready gate.

```text
Proposed Work Item
      ↓
Human Review
      ↓
Ready?
  ┌───┴───┐
  │       │
  No      Yes
  │       │
Stop      ▼
      Coding Workflow
```

This prevents:

```text
Idea captured
→ automatic coding
```

and prevents:

```text
AI decomposition
→ automatic implementation
```

A work item becomes eligible for implementation only after human approval.

---

# 15. Stage Boundaries

## Stage 2A — Local Core

Stage 2A builds the basic ticket-processing engine:

```text
Schema
Validation
Pipeline
Renderer
CLI
FakeDrafter
Automated Tests
```

No live AI is required.

Stage 2A proves that structured ticket data can be validated and rendered correctly.

## Stage 2B — Live AI Integration

Stage 2B connects exactly one approved AI provider.

```text
Raw Idea
  ↓
Real AI
  ↓
Structured Ticket
  ↓
Validation
  ↓
Readable Ticket
```

Stage 2 is not complete until this live flow works successfully.

## Stage 2C — Large Input Decomposition

Stage 2C extends the workflow to support large designs and specifications.

```text
Large Design / Specification
        ↓
AI Analysis
        ↓
Hierarchical Work Breakdown
        ↓
Epic / Story / Task / Other Work Items
        ↓
Validation
        ↓
Human-Readable Proposed Breakdown
```

Stage 2C should preserve:

- hierarchy,
- source traceability,
- human approval,
- stage boundaries.

Stage 2C must not automatically create GitHub Issues.

## Stage 3 — GitHub Integration

Stage 3 connects approved drafts to GitHub.

Conceptually:

```text
Ticket / Work Breakdown Draft
        ↓
Human Review
        ↓
Search Existing GitHub Work
        ↓
Possible Duplicate?
        ↓
Human Approval
        ↓
Create / Update GitHub Work Item
```

GitHub write operations require explicit human approval.

---

# 16. What Stage 2 Must Not Do

Stage 2 must not:

```text
Create GitHub Issues automatically
Modify existing GitHub Issues
Update GitHub Project status
Perform GitHub duplicate search
Start coding automatically
Run multi-agent orchestration
Create pull requests
Auto merge
Deploy
Modify production systems
```

These belong to later stages.

---

# 17. Provider Independence

The architecture should not permanently depend on one AI vendor.

Conceptually:

```text
Idea-to-Ticket
      ↓
Provider Interface
      ↓
Approved AI Provider
```

Stage 2B should implement only one provider for the MVP.

Possible future providers may include:

```text
OpenAI
Anthropic
xAI
Local model
Other compatible providers
```

However, the workflow contract should remain independent of provider brand.

---

# 18. Example — Small Idea

Input:

```text
I want the workflow to support a Parked priority.
```

Expected result:

```text
Single Ticket
```

The AI should not create:

```text
Epic
 └── Story
      └── Task
```

for such a small request unless there is a clear reason.

---

# 19. Example — Large MarketLens Design

Input:

```text
A full MarketLens design containing:

- US market data
- VCP scanner
- market regime
- watchlists
- backtesting
- alerts
- UI
- authentication
```

Possible proposed breakdown:

```text
EPIC: Market Data Platform
 ├── STORY: Historical market data ingestion
 ├── STORY: Local data caching
 └── DATA: Historical backfill

EPIC: Stock Screening
 ├── STORY: VCP detection
 ├── RESEARCH: Confirm VCP rules
 └── TASK: Add scanner tests

EPIC: Market Regime
 ├── RESEARCH: Evaluate regime indicators
 ├── STORY: Classify market conditions
 └── TASK: Implement regime service

EPIC: Watchlists
 ├── STORY: Create watchlist
 └── STORY: Add/remove symbols

EPIC: Backtesting
 ├── ARCHITECTURE: Define backtest engine boundaries
 ├── STORY: Run historical strategy tests
 └── TASK: Generate backtest results
```

This output remains a proposal until reviewed.

---

# 20. Design Principles

The workflow should follow these principles.

## 20.1 Human authority

```text
Human decision > AI recommendation
```

## 20.2 Traceability

Every important generated work item should be explainable back to its source.

## 20.3 Smallest useful decomposition

Do not create unnecessary work items.

## 20.4 Structured output

AI output should follow defined schemas before downstream systems trust it.

## 20.5 Provider independence

Roles and contracts matter more than vendor names.

## 20.6 Stage separation

Idea capture, ticket creation, coding, review, and deployment must remain separate concerns.

## 20.7 No automatic coding from idea capture

```text
Capturing an idea must not automatically turn that idea into code.
```

---

# 21. Future Considerations

The following may be introduced later, but are not required for the first implementation:

```text
Dependency graph between work items
Cross-Epic dependencies
Automatic sizing estimates
Effort / complexity scoring
Risk scoring
Confidence score
Automatic duplicate detection
GitHub sub-issues
GitHub Project hierarchy views
Roadmap generation
Batch document ingestion
Multiple source documents
Requirement traceability matrix
Change-impact analysis
Versioned source references
```

These should only be added after the basic workflow is proven.

---

# 22. Final Architecture Picture

```text
Harry
  ↓
Idea / Design / Specification
  ↓
AI Intake
  ↓
Understand Scope
  ↓
┌─────────────────────────────────────┐
│ Small Input                         │
│   ↓                                 │
│ Single Ticket Draft                 │
│                                     │
│ Large Input                         │
│   ↓                                 │
│ Work Breakdown                      │
│   ↓                                 │
│ Epic → Story → Task / Other Types   │
└─────────────────────────────────────┘
  ↓
Schema Validation
  ↓
Source Traceability
  ↓
Human Review
  ↓
Approve / Edit / Park / Reject
  ↓
GitHub Integration
(Stage 3+)
  ↓
Ready Gate
  ↓
Multi-Agent Coding Workflow
(Stage 4+)
  ↓
Pull Request
  ↓
Human Review
  ↓
Merge
```

---

# 23. Key Rule

The entire architecture should preserve one simple rule:

> **AI may organise work, but AI does not own the roadmap.**

And:

> **AI may propose implementation work, but only a human can authorize that work to begin.**
