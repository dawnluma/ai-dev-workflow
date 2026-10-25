# AI Development Workflow — 由零重新 Setup 指南

> 目標：將以下兩套 workflow 真正落地成一套可以運作嘅 AI Software Development System。
>
> 1. **AI-Powered Idea-to-Ticket Workflow**
> 2. **Multi-Agent Automated Coding Workflow**
>
> 最終流程：
>
> ```text
> Idea
>   ↓
> AI Idea-to-Ticket
>   ↓
> GitHub Issue
>   ↓
> Multi-Agent Coding Workflow
>   ↓
> Pull Request
>   ↓
> Human Review
>   ↓
> Merge
> ```

---

## 1. 最清楚嘅整體 Picture

呢兩套 workflow 唔係兩個獨立 project，而係同一條 development pipeline 嘅前半同後半。

```text
                    HARRY
                      │
                      ▼
                 Raw Idea
          "我想加 Market Regime"
                      │
                      ▼
        ┌────────────────────────┐
        │  ① IDEA-TO-TICKET      │
        │                        │
        │ Understand             │
        │ Classify               │
        │ Duplicate check        │
        │ Priority suggestion    │
        │ Draft ticket           │
        └───────────┬────────────┘
                    │
                    ▼
               Harry Review
                    │
            Approve / Park / Reject
                    │
                    ▼
              GitHub Issue
                    │
              Status = Ready
                    │
════════════════════╪════════════════════
                    │
                    ▼
        ┌────────────────────────┐
        │ ② MULTI-AGENT CODING   │
        │                        │
        │ Planner / Architect    │
        │       ↓                │
        │ Developer              │
        │       ↓                │
        │ Tester                 │
        │       ↓                │
        │ Independent Reviewer   │
        └───────────┬────────────┘
                    │
                    ▼
               Pull Request
                    │
                    ▼
               Harry Review
                    │
                    ▼
                  Merge
```

最重要嘅交接點係：

```text
GitHub Issue
```

**Idea-to-Ticket workflow** 負責 coding 之前嘅工作。

**Multi-Agent Coding workflow** 負責 issue Ready 之後嘅 implementation。

---

# 2. 由零重新 Setup 嘅原則

因為而家準備用一個新 GitHub repository 重新開始，所以唔需要保留舊 setup。

建議：

- 用一個新 repo 專門研究 AI development workflow。
- 暫時唔好將 experimental AI orchestration code 放入 MarketLens。
- MarketLens 將來可以成為第一個真正使用呢套 workflow 嘅 project。
- 每次只完成一個 stage。
- 一個 stage work 同驗證好之後先進下一個 stage。
- 唔好一開始就一次過加入 Claude Code、OpenCode、Ollama、CI、worktree、multi-agent automation。

核心原則：

> **先建立最細可用版本，再逐層增加 automation。**

---

# 3. Repository 建議

建立一個獨立 repo：

```text
ai-dev-workflow
```

建議結構：

```text
ai-dev-workflow/
│
├── idea-to-ticket/
├── orchestrator/
├── prompts/
├── schemas/
├── tests/
├── .ai/
│
├── AGENTS.md
├── README.md
└── .gitignore
```

概念上：

```text
ai-dev-workflow
       │
       │ manages
       ▼
MarketLens
```

而唔係：

```text
MarketLens
└── 一大堆 experimental AI orchestration
```

---

# 4. Stage 1 — 建立 AI Development Workflow Repo

## 目標

只建立乾淨 project foundation。

呢個 stage 暫時唔需要：

```text
Claude Code
OpenCode
Grok API
Ollama
Server
Docker
Database
Multi-Agent Orchestrator
```

## 要做嘅事

1. 建立 GitHub repository：

```text
ai-dev-workflow
```

2. Clone 去 local。

3. 建立基本 folder structure：

```text
idea-to-ticket/
orchestrator/
prompts/
schemas/
tests/
.ai/
```

4. 建立：

```text
README.md
AGENTS.md
.gitignore
```

5. Commit 第一個 baseline。

## Definition of Done

```text
Repository exists
Git works
Local repo works
Main branch clean
Folder structure exists
First commit pushed
```

---

# 5. Stage 2 — Idea-to-Ticket v0

呢個係第一個真正要寫嘅功能。

## 目標

做到：

```text
Raw Idea
   ↓
AI
   ↓
Structured Ticket Draft
```

第一版唔需要 GitHub write access。

## CLI 例子

```bash
python idea.py
```

程式問：

```text
What is your idea?
```

例如輸入：

```text
I want to investigate market breadth for MarketLens.
```

AI 輸出：

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

## 建議 Ticket Types

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

## 建議 Priority

```text
NOW
NEXT
LATER
PARKED
```

AI 可以建議，但唔可以自己決定 roadmap。

## Structured Ticket 建議欄位

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

Example：

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

## Stage 2 暫時唔需要

```text
Claude Code ❌
OpenCode ❌
Grok API ❌
Multi-Agent ❌
Ollama ❌
Server ❌
Docker ❌
Database ❌
```

## Definition of Done

```text
Harry can enter a raw idea
AI returns valid structured output
Output validates against schema
Readable ticket draft is generated
No GitHub Issue is created automatically
No production project is modified
```

---

# 6. Stage 3 — GitHub Integration

Stage 2 work 之後，先接 GitHub。

## 目標

做到：

```text
Idea
 ↓
AI Draft
 ↓
Search GitHub Issues
 ↓
Possible Duplicate?
 ↓
Draft
 ↓
Harry Approve
 ↓
Create GitHub Issue
```

可以先用：

```text
GitHub CLI
gh
```

之後需要先考慮 GitHub API。

## Duplicate Check

新 idea 入嚟之後：

```text
New Idea
   ↓
Search Existing Issues
   ↓
Related Issue?
   /       \
 Yes       No
  │         │
Update     Create
Existing  New Draft
Recommendation
```

AI 只可以建議：

```text
CREATE_NEW
UPDATE_EXISTING
PARK
NEEDS_REVIEW
```

唔可以自動修改 existing issue。

## Human Approval Gate

真正 create GitHub Issue 之前：

```text
Create GitHub Issue? [y/N]
```

Default：

```text
No
```

只有明確 approve 先可以 create。

## Definition of Done

```text
Existing issues can be searched
Possible duplicates can be suggested
Human approval is required
One approved ticket creates one issue
No approval = no write
No coding workflow starts
```

到呢一步：

> **AI-Powered Idea-to-Ticket Workflow 基本完成**

---

# 7. Stage 4 — 簡化版 Multi-Agent Coding

呢一步先正式開始第二套 workflow。

第一版唔好一次過加入所有 automation。

## 目標

只做到：

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

而且可以由一個 orchestrator 順序 handoff。

## 建議角色

重要係角色，而唔係品牌。

概念：

```text
Planner / Architect
        ↓
Developer
        ↓
Tester / Integration
        ↓
Independent Reviewer
```

可以先用你原本設計：

```text
Codex       → Planner
Claude Code → Developer
Cursor      → Tester
Grok        → Reviewer
```

但將來可以替換。

例如：

```text
ChatGPT / Codex
      ↓
Planner

Claude Code
      ↓
Developer

Cursor
      ↓
Tester

Grok
      ↓
Reviewer
```

亦可以之後實驗：

```text
OpenCode
   ↓
Developer
```

---

# 8. Stage 5 — 將兩套 Workflow 接埋

到呢度先真正變成一條完整 pipeline：

```text
Idea
 ↓
AI Intake
 ↓
GitHub Issue
 ↓
Ready
 ↓
AI Coding Team
 ↓
Pull Request
```

必須保留一個重要 gate：

```text
GitHub Issue
     ↓
   Ready?
   /    \
 No      Yes
 │        │
Stop      ▼
       Coding Team
```

原因：

> 一個新 idea 唔應該因為被記錄咗，就自動變成 code change。

---

# 9. Stage 6 — Advanced Automation

成套 basic pipeline 已經 work 之後先做。

## Feedback Loop

```text
Reviewer FAIL
    ↓
Developer Fix
    ↓
Tester Retest
    ↓
Reviewer Review
```

## 再加入

```text
Structured JSON
Retry limits
Error handling
Logging
Token / cost tracking
Run history
```

## 再之後

```text
Git branches
Git worktrees
Parallel agents
```

## 再之後

```text
GitHub Issue
    ↓
Agent Team
    ↓
Pull Request
```

## 最後先做

```text
CI FAIL
   ↓
AI Fix
   ↓
CI Retest
```

最後先考慮：

```text
Auto Merge
Auto Deploy
Production Changes
```

---

# 10. 你而家已有嘅 Tools

而家已經有：

```text
ChatGPT ✅
Cursor  ✅
Grok    ✅
GitHub  ✅
```

暫時唔需要急住加：

```text
Claude Code ⏳
OpenCode    ⏳
Ollama      ⏳
Cloud VM    ⏳
```

---

# 11. Claude Code 幾時先需要？

唔需要一開始就 subscribe。

比較合理係去到：

```text
Stage 4
Multi-Agent Coding Workflow
```

先決定係咪需要 Claude Code 做 Developer role。

因為 Stage 1–3 主要係：

```text
Repo setup
Idea intake
Structured ticket
GitHub integration
```

未需要 multi-agent coding。

---

# 12. OpenCode 幾時先需要？

OpenCode 可以 local run。

但第一步唔需要。

將來可以有：

```text
OpenCode
   ↓
Cloud model
```

或者：

```text
OpenCode
   ↓
Ollama
   ↓
Local model
```

OpenCode 比較適合 Stage 4 之後，用嚟研究：

```text
Planner
Developer
Tester
Reviewer
```

其中某一個 role 可唔可以由 provider-neutral/local agent 做。

---

# 13. Ollama 幾時先需要？

更後期。

因為：

```text
OpenCode local
≠
AI model local
```

真正 fully-local 會係：

```text
OpenCode
   ↓
Ollama
   ↓
Local LLM
```

但 local model 嘅：

```text
RAM
GPU
Tool calling
Coding quality
Context size
```

都要另外處理。

所以建議：

```text
Cloud model first
↓
Workflow works
↓
再加入 Local Model
```

---

# 14. 最簡單 Roadmap

```text
┌──────────────────────────────┐
│ Stage 1                      │
│ 建 ai-dev-workflow repo      │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Stage 2                      │
│ Raw Idea → Structured Ticket │
│ 暫時 local                   │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Stage 3                      │
│ GitHub Issue integration     │
│ Duplicate check + approval   │
└──────────────┬───────────────┘
               ↓
      IDEA-TO-TICKET DONE ✅
               ↓
┌──────────────────────────────┐
│ Stage 4                      │
│ Planner → Developer          │
│ → Tester → Reviewer          │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Stage 5                      │
│ GitHub Issue → Agent Team    │
│ → Pull Request               │
└──────────────┬───────────────┘
               ↓
      FULL PIPELINE DONE ✅
               ↓
┌──────────────────────────────┐
│ Stage 6                      │
│ Retry / CI / worktree /      │
│ cost / parallel / automation │
└──────────────────────────────┘
```

---

# 15. 今日應該做嘅只有一件事

今日只做：

> **Stage 1 — 建立一個全新 `ai-dev-workflow` repository 同基本 project structure。**

今日唔好一次過：

```text
Install Claude Code
Install OpenCode
Install Ollama
Build orchestrator
Build GitHub automation
Modify MarketLens
```

第一張真正畀呢套 workflow 處理嘅 ticket，可以就係：

```text
Build the Idea-to-Ticket MVP
```

概念：

```text
你先人手建立第一張 ticket
           ↓
Build Idea-to-Ticket
           ↓
之後所有 ticket
可以由 Idea-to-Ticket 幫你產生
           ↓
再由 Multi-Agent Pipeline 寫 code
```

---

# 16. 最終成功 Picture

最後你想做到：

```text
Harry
  ↓
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
Planner
  ↓
Developer
  ↓
Tester
  ↓
Independent Reviewer
  ↓
Pull Request
  ↓
Harry Review
  ↓
Merge
```

最重要嘅 design principle：

> **Capturing an idea must not automatically turn that idea into code.**

第二個 principle：

> **每次只完成一個 stage，驗證成功先進下一個 stage。**

第三個 principle：

> **角色比工具品牌更重要。**

今日真正要開始嘅地方：

```text
Stage 1
建立 ai-dev-workflow repo
```
