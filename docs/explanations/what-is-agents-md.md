# What Is `AGENTS.md`?

## Overview

`AGENTS.md` 可以理解做：

> **俾 AI coding agents 睇嘅「公司守則 + Project Instruction Manual」。**

如果 `README.md` 主要係俾人類睇，講個 project 係乜、點樣 setup、點樣使用，

咁 `AGENTS.md` 就主要係俾 AI coding tools / agents 睇，話畀佢哋知：

- 呢個 project 係做咩
- 做嘢時要遵守咩規則
- 邊啲嘢可以做
- 邊啲嘢唔可以做
- 每個角色負責咩
- 點樣測試
- Git 應該點用
- 有咩 security boundary
- 做完一個 task 要符合咩 Definition of Done


如果 repository 同時使用 Claude Code，可以另外有：

```text
CLAUDE.md
```

但兩者用途唔同：

```text
AGENTS.md
= 所有 AI agents 共用嘅 repository-wide rules

CLAUDE.md
= Claude Code-specific entry point
```

所以 `CLAUDE.md` 唔係用嚟取代 `AGENTS.md`。

---

# 1. 點解需要 `AGENTS.md`？

當你用 AI coding tools，例如：

```text
Claude Code
Cursor
Codex
OpenCode
Grok
```

如果每次開新 task 都要重新講一次：

```text
唔好直接 push main
要寫 tests
唔好掂 production
只做今次 Stage
唔好自己 auto merge
唔好 commit secrets
```

會非常重複。

所以可以將呢啲長期規則全部放落：

```text
AGENTS.md
```

之後每次 AI 開始做嘢之前，先讀 `AGENTS.md`。

如果用 Claude Code，就再由 `CLAUDE.md` 提供 Claude-specific 嘅入口指示，例如：

```text
Read AGENTS.md
Read README.md
Read .ai/task.md when a task exists
Execute only the requested stage
```

即係：

```text
AGENTS.md
→ Universal project rules

CLAUDE.md
→ Claude Code entry point
```

咁就唔需要每次重新解釋成套 project rules。

---

# 2. `AGENTS.md` 主要負責咩？

你而家呢個 `ai-dev-workflow` project 入面，`AGENTS.md` 主要負責以下幾類規則。

## Project Purpose

例如：

```text
This repository implements an AI-assisted software development workflow.
```

等 AI 知道：

```text
呢個 repo
≠ MarketLens production repo
```

而係：

```text
AI development workflow project
```

---

## Human Approval Boundary

例如：

```text
AI may draft a GitHub Issue.
AI must not automatically start implementation.
```

或者：

```text
AI cannot mark a ticket Ready without human approval.
```

咁 AI 就知道邊度一定要停低等你決定。

---

## Stage Rules

例如你話：

```text
Execute Stage 2 only.
```

`AGENTS.md` 可以規定：

```text
Do not start Stage 3.
Do not implement future infrastructure.
Stop after Stage 2 is complete.
```

咁可以避免 AI 太積極，一次過做晒成個 roadmap。

---

## Coding Rules

例如：

```text
Keep functions small.
Avoid unnecessary dependencies.
Do not modify unrelated files.
Add tests for new behaviour.
```

---

## Git Rules

例如：

```text
Do not push directly to main.
Do not force push.
Do not rewrite unrelated history.
Do not auto merge.
```

---

## Security Rules

例如：

```text
Never commit:
.env
API keys
GitHub tokens
Passwords
Credentials
```

---

## Testing Rules

例如：

```text
Build must pass.
Tests must pass.
Lint must pass.
Type checking must pass.
```

AI 唔可以淨係話：

```text
"It looks fine."
```

而係要有 objective evidence。

---

# 3. `AGENTS.md` 同其他文件有咩分別？

最簡單可以咁理解：

```text
README.md
→ 告訴人類呢個 project 係乜、點用

AGENTS.md
→ 告訴所有 AI agents 做呢個 project 時要守咩規矩

CLAUDE.md
→ 告訴 Claude Code 點樣進入同執行呢個 repository 嘅工作

.ai/task.md
→ 今次具體要做乜

.ai/plan.md
→ Planner 今次打算點做

.ai/test-report.md
→ Tester 實際驗證結果

.ai/review.md
→ Reviewer 最後評估
```

---

# 3A. `AGENTS.md` 同 `CLAUDE.md` 有咩分別？

呢個 project 而家同時會有兩份文件：

```text
AGENTS.md
CLAUDE.md
```

最重要嘅分別係：

```text
AGENTS.md
= Vendor-neutral
= 所有 agents 共用
= Repository-wide operating rules
```

而：

```text
CLAUDE.md
= Claude Code-specific
= Claude Code 嘅 entry point
= 指示 Claude Code 先讀邊啲文件同點樣執行 task
```

例如：

```text
AGENTS.md
```

可以寫：

```text
Do not push directly to main.
Do not auto merge.
Do not commit secrets.
Complete one stage at a time.
Human approval is required before Ready / merge / deploy.
```

而：

```text
CLAUDE.md
```

可以寫：

```text
Read AGENTS.md first.
Read README.md.
Read .ai/task.md when present.
Claude Code's primary role is Developer.
Do not implement future stages early.
```

咁樣有一個好處：

```text
Claude Code
Cursor
Codex
OpenCode
Grok
其他 future agents
```

都可以共享同一份：

```text
AGENTS.md
```

而每個 tool 如果有自己嘅特殊入口文件，再另外處理。

所以設計原則係：

> **Universal rules 放 `AGENTS.md`；tool-specific instructions 放相應 tool-specific file。**

---

# 4. 可以當成「公司 Employee Handbook」

一個好容易理解嘅比喻係：

```text
AGENTS.md
=
公司 Employee Handbook
```

例如公司 handbook 會寫：

```text
唔可以直接改 production
唔可以洩漏 password
所有 change 要 review
所有 feature 要 test
```

而：

```text
task.md
```

就等於：

```text
今日老細派畀你嘅工作
```

例如：

```text
Task:
Build Stage 2 Idea-to-Ticket CLI.
```

AI 做今次工作時，就要同時遵守：

```text
AGENTS.md
+
task.md
```

如果今次係 Claude Code 執行，就會變成：

```text
AGENTS.md
+
CLAUDE.md
+
task.md
```

可以理解成：

```text
AGENTS.md
= 公司 Employee Handbook

CLAUDE.md
= Claude Code 入職 / 操作指引

task.md
= 今日派畀佢嘅工作
```

---

# 5. 一次 Task 嘅完整流程

例如：

```text
AGENTS.md
   ↓
Project-wide rules

CLAUDE.md (when Claude Code is used)
   ↓
Claude-specific entry instructions

task.md
   ↓
今次工作要求

Planner
   ↓
plan.md

Developer
   ↓
Implementation

Tester
   ↓
test-report.md

Reviewer
   ↓
review.md
```

即係：

```text
AGENTS.md
```

唔係一張 task。

佢係所有 task 都要遵守嘅長期規則。

---

# 6. Example

假設 `AGENTS.md` 寫：

```text
Never push directly to main.
Add tests for all new behaviour.
Do not implement future stages.
Do not modify MarketLens production code.
```

而今次 `task.md` 寫：

```text
Build Stage 2 Idea-to-Ticket CLI.
```

AI 應該理解成：

```text
我要完成 Stage 2。

但同時：

- 唔可以做 Stage 3
- 唔可以 create GitHub Issue automation
- 唔可以掂 MarketLens production
- 要有 tests
- 唔可以直接 push main
```

呢個就係 `AGENTS.md` 嘅價值。

---

# 7. `AGENTS.md` 應該放邊？

建議放喺 repository root：

```text
ai-dev-workflow/
│
├── AGENTS.md
├── CLAUDE.md
├── README.md
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

> 呢份文件係成個 repository 嘅 general AI development rules。

---

# 8. `AGENTS.md` 係咪寫一次就唔再改？

唔係。

`AGENTS.md` 應該隨住 project 慢慢更新。

例如後來你發現一個新問題：

```text
AI 唔應該自動將 GitHub Project item 由 Next 改做 Ready。
```

就可以加：

```text
AI must not move a GitHub Project item to Ready without explicit human approval.
```

又例如你之後決定：

```text
All Python changes must run:
pytest
ruff
mypy
```

亦可以加返落去。

所以 `AGENTS.md` 會慢慢變成：

> **你成套 AI Development System 嘅長期 Operating Rules。**

---

# 9. 同 AI Brand 無直接綁定

`AGENTS.md` 最好保持 vendor-neutral，唔好只為某一個 AI 寫死。

例如唔應該只寫：

```text
Claude must do X.
Cursor must do Y.
```

而應該先定義：

```text
Planner
Developer
Tester
Reviewer
```

再由唔同工具去扮演角色。

例如目前建議：

```text
Harry           → Repository Owner / Final Authority
ChatGPT / Codex → Planner / Architect
Claude Code     → Primary Developer / Implementation Agent
Cursor          → Integration / Tester / IDE
Grok            → Independent Reviewer
```

將來亦可以變成：

```text
Planner     → OpenCode
Developer   → Claude Code
Tester      → another agent
Reviewer    → GPT
```

所以：

> **角色比工具品牌重要。**

目前使用策略可以係：

```text
Stage 1
Claude Code optional

Stage 2–3
Claude Code 可作 Primary Implementation Agent

Stage 4
Planner → Developer → Tester → Reviewer
正式成為 multi-agent workflow
```

即係「Claude Code 由 Stage 2 開始幫手寫 code」同「Stage 4 正式 multi-agent」係兩件唔同嘅事。

---

# 10. 對你而家個 Project 最重要嘅用途

你而家個 project 最重要嘅三條 `AGENTS.md` 原則係：

## Rule 1

```text
Capturing an idea must not automatically turn that idea into code.
```

即係：

```text
Idea
↓
Ticket
↓
Human Approval
↓
Ready
↓
先至 Development
```

---

## Rule 2

```text
Complete and verify one stage before moving to the next.
```

即係：

```text
Stage 1
↓
Verify
↓
Stage 2
↓
Verify
↓
Stage 3
```

而唔係一次過做晒。

---

## Rule 3

```text
Roles and verification boundaries matter more than AI brand names.
```

即係：

```text
Planner
Developer
Tester
Reviewer
```

呢四個 responsibilities 先係 architecture 核心。

用邊個 AI 做，可以之後再換。

---


---

## Rule 4

```text
No AI agent is the repository owner.
Human authority remains final.
```

即係：

```text
AI
→ 可以 plan
→ 可以 implement
→ 可以 test
→ 可以 review
→ 可以 recommend

Harry / Human
→ 決定 priority
→ 決定 Ready
→ 決定 merge
→ 決定 deployment
→ 最終 authority
```

Claude Code、Cursor、Grok 或其他 agents 都只係 workflow 入面嘅角色，唔係 repository owner。

---

# 11. 總結

你可以用一句說話記住：

> **README.md 係俾人睇，AGENTS.md 係俾 AI team 睇。**

更加完整少少：

```text
README.md
= Project introduction

AGENTS.md
= AI team operating rules for all agents

CLAUDE.md
= Claude Code-specific entry point

task.md
= 今次做咩

plan.md
= 今次點做

test-report.md
= 做完係咪真係 work

review.md
= 最後獨立 review
```

所以 `AGENTS.md` 唔係用嚟記錄某一張 ticket。

佢係：

> **所有 AI agents 喺呢個 repository 工作時都應該遵守嘅長期規則。**

而 Claude Code 額外由 `CLAUDE.md` 取得自己嘅 tool-specific entry instructions，但最終仍然要服從 `AGENTS.md`。
