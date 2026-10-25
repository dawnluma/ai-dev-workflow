# What Is `CLAUDE.md`?

## Overview

`CLAUDE.md` 可以理解做：

> **Claude Code 進入一個 repository 時嘅專屬 Entry Point / Operating Guide。**

如果：

```text
README.md
```

主要係俾人類睇，

而：

```text
AGENTS.md
```

係俾所有 AI coding agents 睇嘅 repository-wide rules，

咁：

```text
CLAUDE.md
```

就係：

> **專門俾 Claude Code 睇嘅 tool-specific instructions。**

最簡單可以咁理解：

```text
README.md
= 呢個 project 係乜

AGENTS.md
= 所有 AI agents 要守咩規矩

CLAUDE.md
= Claude Code 進入呢個 repo 時要點做
```

---

# 1. 點解需要 `CLAUDE.md`？

Claude Code 係一個可以直接喺 repository 入面工作嘅 coding agent。

佢可能會：

```text
Read files
Inspect code
Edit multiple files
Run shell commands
Run tests
Use Git
Inspect diffs
Implement tasks
```

所以如果每次開始一個新 task 都要重新講：

```text
先讀 AGENTS.md
先讀 README.md
有 task 就讀 .ai/task.md
只做指定 Stage
唔好自動做下一個 Stage
唔好 auto merge
做完要 run tests
```

會好重複。

所以可以將 Claude Code-specific 嘅入口規則放入：

```text
CLAUDE.md
```

之後 Claude Code 進入 repository 時，就有一個固定嘅 starting point。

---

# 2. `CLAUDE.md` 唔係取代 `AGENTS.md`

呢個係最重要嘅概念。

你個 project 應該保持：

```text
AGENTS.md
= Universal / Vendor-neutral rules
```

而：

```text
CLAUDE.md
= Claude Code-specific instructions
```

兩者關係可以畫成：

```text
              Repository
                  │
                  ▼
             AGENTS.md
                  │
        Universal AI Rules
                  │
                  ▼
             CLAUDE.md
                  │
        Claude Code Entry Point
                  │
                  ▼
             .ai/task.md
                  │
             Current Task
```

所以 Claude Code 唔應該只睇 `CLAUDE.md` 就忽略 `AGENTS.md`。

正確做法係：

```text
Claude Code
   ↓
Read CLAUDE.md
   ↓
Read AGENTS.md
   ↓
Read README.md
   ↓
Read .ai/task.md if present
   ↓
Execute current task
```

---

# 3. `AGENTS.md` 同 `CLAUDE.md` 有咩分別？

## `AGENTS.md`

負責：

```text
Project-wide rules
Human approval boundaries
Git rules
Security rules
Testing rules
Stage boundaries
Coding rules
Role responsibilities
Definition of Done
```

例如：

```text
Do not push directly to main.
Do not auto merge.
Do not commit secrets.
Do not modify production without approval.
Complete one stage before starting the next.
```

呢啲規則唔應該只屬於 Claude Code。

Cursor、Codex、OpenCode、Grok 或其他 future agents 都應該遵守。

---

## `CLAUDE.md`

負責 Claude Code-specific 嘅入口指示。

例如：

```text
Read AGENTS.md first.
Read README.md.
Read .ai/task.md if it exists.
Claude Code's primary role is Developer.
Do not implement future stages early.
Run relevant tests before reporting completion.
```

所以：

```text
AGENTS.md
= Company Handbook

CLAUDE.md
= Claude Code-specific onboarding / operating guide
```

---

# 4. 點解唔將所有規則全部寫入 `CLAUDE.md`？

因為咁樣會將成個 repository 綁死喺 Claude Code。

例如如果所有 rules 都只放：

```text
CLAUDE.md
```

咁將來你改用：

```text
Cursor
Codex
OpenCode
Another coding agent
```

就可能要重新複製一次同樣嘅 rules。

更好嘅設計係：

```text
Universal Rules
        ↓
     AGENTS.md

Claude-specific
        ↓
     CLAUDE.md
```

將來如果另一個 tool 有自己嘅 special instructions，亦可以另外處理。

---

# 5. 你個 Repository 入面應該放邊？

建議放喺 repository root：

```text
ai-dev-workflow/
│
├── README.md
├── AGENTS.md
├── CLAUDE.md
├── .env.example
├── .gitignore
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
└── .ai/
```

原因係：

> Claude Code 一進入 repository，就應該可以好容易搵到佢嘅 project-level instructions。

---

# 6. Claude Code 喺呢個 Project 嘅角色

目前建議角色分工：

```text
Harry
→ Repository Owner / Final Authority

ChatGPT / Codex
→ Planner / Architect

Claude Code
→ Primary Developer / Implementation Agent

Cursor
→ Integration / Tester / IDE

Grok
→ Independent Reviewer
```

所以 Claude Code 主要負責：

```text
Read approved task
Read implementation plan
Inspect repository
Implement requested change
Add or update tests
Run relevant validation
Fix implementation errors
Report what changed
```

但 Claude Code 唔係 final authority。

---

# 7. Claude Code 唔可以自己決定啲乜？

Claude Code 可以：

```text
Implement
Refactor within scope
Add tests
Run tests
Inspect diffs
Fix bugs
Recommend changes
```

但唔應該自己決定：

```text
What should be prioritised
Whether an issue is Ready
Whether code should be merged
Whether production should be deployed
Whether roadmap priority should change
Whether an idea should automatically become code
```

所以：

```text
Claude Code
= Worker / Developer

Harry
= Owner / Final Authority
```

---

# 8. Claude Code 幾時開始用？

目前建議：

```text
Stage 1
Repository Foundation
→ Claude Code optional

Stage 2
Idea-to-Ticket MVP
→ Claude Code recommended as Primary Developer

Stage 3
GitHub Integration
→ Claude Code continues as Primary Developer

Stage 4
Multi-Agent Coding Workflow
→ Claude Code formally occupies Developer role
```

---

# 9. Stage 1 點用 Claude Code？

Stage 1 主要係：

```text
Create repository
Create folders
Add README.md
Add AGENTS.md
Add CLAUDE.md
Add docs/
Add examples/
Add .env.example
Add .gitignore
Create baseline commit
```

Stage 1 唔需要依賴 Claude Code。

可以：

```text
Use Cursor manually
Use Git manually
Use ChatGPT for document design
```

但預先建立：

```text
CLAUDE.md
```

有一個好處：

> Stage 2 一開始用 Claude Code，就已經有明確 operating rules。

---

# 10. Stage 2 點用 Claude Code？

Stage 2 係第一個真正 implementation stage。

目標：

```text
Raw Idea
   ↓
Structured Ticket Draft
```

呢個時候 Claude Code 可以做：

```text
Create Python modules
Create schema models
Create CLI
Create tests
Run pytest
Run lint
Fix implementation
```

但要遵守：

```text
Execute Stage 2 only.
```

即係唔可以提早加入：

```text
GitHub issue creation
Duplicate search
Multi-agent orchestrator
CI automation
```

因為呢啲屬於後面 stages。

---

# 11. Stage 3 點用 Claude Code？

Stage 3 會加入：

```text
GitHub issue search
Duplicate detection
Human approval
Issue creation
```

Claude Code 可以負責：

```text
Implement GitHub integration
Implement approval prompt
Implement duplicate search
Add tests
Handle failures
```

但：

```text
Claude Code can implement the mechanism
```

唔等於：

```text
Claude Code can approve the action
```

真正 write GitHub 之前仍然需要：

```text
Create GitHub Issue? [y/N]
```

而 default：

```text
No
```

---

# 12. Stage 4 點用 Claude Code？

Stage 4 先正式開始 multi-agent workflow：

```text
task.md
  ↓
Planner
  ↓
plan.md
  ↓
Developer
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

目前建議：

```text
ChatGPT / Codex
→ Planner

Claude Code
→ Developer

Cursor
→ Tester / Integration

Grok
→ Reviewer
```

Claude Code 喺呢個 stage 會正式成為：

> **Developer role**

---

# 13. `CLAUDE.md` 同 `.ai/` 有咩關係？

`.ai/` 係 workflow handoff artifacts。

例如：

```text
.ai/
├── task.md
├── plan.md
├── implementation.md
├── test-report.md
└── review.md
```

而：

```text
CLAUDE.md
```

係長期 operating guide。

所以：

```text
CLAUDE.md
= Claude Code 長期點樣工作

.ai/task.md
= 今次要做乜

.ai/plan.md
= 今次打算點做

.ai/test-report.md
= 實際驗證結果

.ai/review.md
= 最後 review
```

---

# 14. 一次 Claude Code Task 嘅完整流程

可以係：

```text
Claude Code starts
   ↓
Read CLAUDE.md
   ↓
Read AGENTS.md
   ↓
Read README.md
   ↓
Read .ai/task.md
   ↓
Read .ai/plan.md if present
   ↓
Inspect repository
   ↓
Implement
   ↓
Run tests
   ↓
Inspect diff
   ↓
Report result
```

如果係 Stage 4：

```text
Planner
   ↓
plan.md
   ↓
Claude Code
   ↓
Implementation
   ↓
Cursor
   ↓
test-report.md
   ↓
Grok
   ↓
review.md
```

---

# 15. `CLAUDE.md` 應該寫啲乜？

一份好嘅 `CLAUDE.md` 通常可以包括：

```text
Purpose
Files to read first
Claude Code's role
Role boundaries
Stage execution rules
Human approval boundaries
Repository scope
Security rules
Coding behaviour
Testing expectations
Git behaviour
Destructive action rules
.ai workflow usage
Completion report format
```

但唔應該大量重複：

```text
AGENTS.md
```

例如唔需要將成份 universal Git rules、security rules、ticket model 全部 duplicate 一次。

應該盡量：

```text
CLAUDE.md
→ point to AGENTS.md
```

而唔係：

```text
CLAUDE.md
→ become another giant copy of AGENTS.md
```

---

# 16. `CLAUDE.md` 可唔可以公開？

可以。

如果 repository 本身係 public，`CLAUDE.md` 亦可以 public。

但內容要保持 generic。

可以公開：

```text
Read AGENTS.md
Run tests
Do not auto merge
Developer role
Stage boundaries
Completion report format
```

唔好放：

```text
API keys
Passwords
Access tokens
Private repository URLs
Internal company URLs
Customer data
Private credentials
Personal machine-specific secrets
Production configuration
```

所以：

```text
CLAUDE.md
```

本身唔係 secret file。

---

# 17. `CLAUDE.md` 係咪寫一次就唔再改？

唔係。

佢應該隨住 workflow 成熟而更新。

例如將來你可能加入：

```text
Specific test commands
Branch naming rules
PR creation procedure
CI retry behaviour
Worktree usage
Structured completion output
```

如果呢啲係 Claude Code-specific 行為，就可以放：

```text
CLAUDE.md
```

如果係所有 agents 都應該遵守，就應該放：

```text
AGENTS.md
```

一個簡單 decision rule：

```text
所有 agents 都應該守？
        │
      Yes
        ↓
    AGENTS.md

只係 Claude Code-specific？
        │
      Yes
        ↓
    CLAUDE.md
```

---

# 18. 同 AI Brand 嘅關係

雖然文件叫：

```text
CLAUDE.md
```

但成個 architecture 唔應該依賴 Claude 呢個品牌。

真正 architecture 係：

```text
Planner
Developer
Tester
Reviewer
```

目前只係：

```text
Planner     → ChatGPT / Codex
Developer   → Claude Code
Tester      → Cursor
Reviewer    → Grok
```

將來可以改：

```text
Developer   → OpenCode
Reviewer    → GPT
Tester      → another coding agent
```

所以：

> **Role is architectural. Tool assignment is replaceable.**

---

# 19. 點解呢個設計適合你個 Project？

你而家最想建立嘅唔係：

```text
Claude Code project
```

而係：

```text
AI Development Workflow
```

所以最健康嘅結構係：

```text
AGENTS.md
→ Long-term vendor-neutral rules

CLAUDE.md
→ Claude Code-specific entry point

.ai/
→ Runtime task handoff artifacts

README.md
→ Human-facing project explanation
```

咁就唔會因為將來轉 AI tool，而要重寫成個 project architecture。

---

# 20. 最重要嘅幾條原則

## Rule 1

```text
CLAUDE.md does not replace AGENTS.md.
```

---

## Rule 2

```text
Claude Code is an implementation agent,
not the repository owner.
```

---

## Rule 3

```text
Complete only the requested stage.
```

---

## Rule 4

```text
Human approval remains final for:
Priority
Ready
Merge
Deployment
Production changes
```

---

## Rule 5

```text
Capturing an idea must not automatically
turn that idea into code.
```

---

# 21. 總結

最簡單可以記住：

```text
README.md
= Project introduction

AGENTS.md
= AI team operating rules

CLAUDE.md
= Claude Code-specific entry point

.ai/task.md
= 今次做咩

.ai/plan.md
= 今次點做

.ai/test-report.md
= 做完係咪真係 work

.ai/review.md
= 最後獨立 review
```

所以：

> **`CLAUDE.md` 係 Claude Code 進入呢個 repository 時嘅專屬 operating guide，但佢仍然要服從 `AGENTS.md` 嘅 repository-wide rules。**

而你個 project 嘅核心 ownership 仍然係：

```text
Harry / Human
→ Final Authority
```
