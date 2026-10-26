# Stage 2 — Idea-to-Ticket MVP 到底做緊乜？

> 呢份文件係畀 Harry 用嚟理解 `ai-dev-workflow` Stage 2 嘅「人話版」說明。
>
> 目的唔係再寫一份 implementation plan，而係解釋：**點解要做、每個 file 做乜、AI 喺邊度出現、Cursor 會寫乜、你要決定乜、完成標準係乜。**

---

## 1. 一句講晒 Stage 2

Stage 2 要整一個本機小工具，做到：

```text
Harry 輸入一個 idea
        ↓
程式將 idea 交畀 AI
        ↓
AI 將 idea 寫成結構化 ticket
        ↓
程式檢查 ticket 格式係咪正確
        ↓
程式將 ticket 顯示成容易睇嘅文字
```

例如你輸入：

```text
I want the workflow to detect duplicate ideas before new work is started.
```

最後可能得到：

```text
Title:
Add Duplicate Idea Detection

Type:
FEATURE

Priority:
NEXT

Problem:
New ideas may duplicate existing work.

Ready for Development:
No

Recommended Action:
Review duplicate detection design before implementation.
```

Stage 2 **只係產生 ticket draft**。

佢暫時唔會：

- 自動建立 GitHub Issue
- 自動搜尋 GitHub Issue
- 自動改 GitHub Project status
- 自動開始寫 code
- 自動開 Pull Request
- 自動改 production project

呢啲係之後 Stage 3、Stage 4、Stage 5 嘅工作。

---

# 2. 點解 Stage 2 分做 2A 同 2B？

而家個 plan 將 Stage 2 拆做：

```text
Stage 2A — Local Core
Stage 2B — Live AI Integration
```

呢個拆法係好重要嘅。

## Stage 2A — Local Core

Stage 2A **暫時唔 call 真 AI**。

佢先建立：

```text
Ticket Schema
Validation
Renderer
Pipeline
CLI
Tests
Fake AI response
```

目的係先證明：

> 「如果 AI 畀我一個 ticket JSON，我個程式有能力檢查佢、拒絕錯格式、再顯示一張正常 ticket。」

可以理解成先起好「機器」。

```text
         暫時冇真 AI
              │
              ▼
Fake Ticket JSON
      ↓
Validation
      ↓
Readable Ticket
```

Stage 2A 完成之後，個程式骨架已經 work，但仲未做到：

```text
Raw Idea → 真 AI → Ticket
```

所以 **Stage 2A 完成 ≠ Stage 2 完成**。

## Stage 2B — Live AI Integration

Stage 2B 先加入一個真正 AI provider，例如將來你 approve：

```text
OpenAI
```

或者：

```text
Anthropic
```

只揀一個。

到時真正流程係：

```text
Harry
  ↓
Raw Idea
  ↓
idea_to_ticket CLI
  ↓
Prompt + Idea
  ↓
Real AI
  ↓
JSON Ticket
  ↓
Validation
  ↓
Readable Ticket
```

做到呢個 live end-to-end test 成功，Stage 2 先真正完成。

---

# 3. 點解唔一開始就直接 call AI？

因為如果一開始全部混埋一齊：

```text
CLI
AI API
Prompt
JSON parsing
Validation
Rendering
Environment variables
API key
```

有問題時會好難知道係邊一層出錯。

所以 Stage 2A 先將 AI 拆走，用固定資料測試核心流程。

等核心流程完全 work：

```text
Schema ✅
Validation ✅
Rendering ✅
CLI ✅
Tests ✅
```

Stage 2B 先插入真 AI。

---

# 4. Repository 最後大概會變成點？

```text
ai-dev-workflow/
│
├── pyproject.toml
│
├── idea_to_ticket/
│   ├── __init__.py
│   ├── idea.py
│   ├── pipeline.py
│   ├── provider.py
│   └── render.py
│
├── prompts/
│   └── idea_intake.md
│
├── schemas/
│   ├── __init__.py
│   └── ticket.py
│
├── examples/
│   ├── sample-idea.md
│   └── sample-ticket.json
│
└── tests/
    ├── test_ticket_schema.py
    ├── test_render.py
    └── test_pipeline.py
```

Human-facing workflow 繼續叫：

```text
Idea-to-Ticket
```

Python package 就叫：

```text
idea_to_ticket
```

---

# 5. 每一個 file 到底做乜？

## `idea_to_ticket/idea.py`

呢個係你直接使用嘅入口。

將來你 run：

```bash
python -m idea_to_ticket.idea
```

佢可能問：

```text
What is your idea?
```

`idea.py` 本身唔負責「諗 ticket」。

佢主要負責：

```text
收 idea
  ↓
搵 AI provider
  ↓
叫 pipeline 處理回覆
  ↓
叫 renderer 顯示 ticket
```

可以當佢係 receptionist / coordinator。

## `idea_to_ticket/provider.py`

呢個 file 負責「點樣同 AI 傾計」。

概念：

```text
provider.complete(prompt, idea)
```

輸入：

```text
prompt + Harry's raw idea
```

輸出：

```text
AI 原始回覆文字
```

Stage 2A 有 `FakeDrafter`；Stage 2B 先有 real provider。

## `prompts/idea_intake.md`

呢個 file 係畀 AI 嘅 instruction。

例如會要求 AI：

```text
分析 raw idea
只輸出指定 JSON structure
type 只可以係指定類型
priority 只可以係 now / next / later / parked
duplicate_of 喺 Stage 2 必須係 null
```

將 prompt 放入 file，而唔 hard-code 落 Python，方便 version control、review 同日後調整。

## `schemas/ticket.py`

呢個係 ticket 嘅「正式規格」。

例如：

```text
title 一定要有
priority 只可以係 now / next / later / parked
ready_for_development 一定係 true / false
duplicate_of Stage 2 一定係 null
```

可以當佢係 **Ticket Contract**。

## `idea_to_ticket/pipeline.py`

Pipeline 負責：

```text
AI raw text
   ↓
移除 optional Markdown code fence
   ↓
json.loads()
   ↓
Pydantic validation
   ↓
Validated TicketDraft
```

如果 parse 或 validation fail，就停止，唔會將錯資料當成正常 ticket。

## `idea_to_ticket/render.py`

AI 負責內容；renderer 負責固定顯示格式。

例如 AI 產生 JSON，renderer 再變成：

```text
Title:
...

Type:
FEATURE

Priority:
NEXT
```

咁每次 output 都一致。

## `examples/sample-ticket.json`

一張已知正確嘅 sample ticket，用嚟測試完整 core pipeline，但唔需要真 AI。

## `tests/`

用 `pytest` 驗證：

```text
合法 ticket → PASS
缺 title → FAIL
亂 priority → FAIL
extra field → FAIL
non-null duplicate_of → FAIL
renderer output → 穩定
pipeline garbage input → FAIL
```

---

# 6. FakeDrafter 係乜？

`FakeDrafter` 係測試 double。

佢扮 AI 回固定答案，令 automated tests：

```text
唔需要 API key
唔需要 internet
唔使用 AI credits
唔受 AI 每次回覆唔同影響
```

所以：

```text
pytest
```

可以快速同 deterministic 咁 run。

---

# 7. 點解用 Pydantic？

Pydantic 做 data validation。

例如 AI 回：

```json
{
  "priority": "urgentest"
}
```

但合法 priority 只有：

```text
now
next
later
parked
```

Pydantic 就會 reject。

可以記住：

> **AI 係作者，Pydantic 係守門員。**

---

# 8. `--fixture`、`--idea`、`--json` 係乜？

## `--fixture`

```bash
python -m idea_to_ticket.idea --fixture examples/sample-ticket.json
```

跳過 AI，直接：

```text
sample JSON → validation → renderer
```

主要用嚟證明 Stage 2A work。

## `--idea`

```bash
python -m idea_to_ticket.idea --idea "Add duplicate detection"
```

直接由 command line 傳 idea，唔使 interactive prompt。

## `--json`

顯示 validated structured JSON，而唔係 human-readable version。

日後 Stage 3 會更容易 reuse structured output。

---

# 9. 點解用 `python -m idea_to_ticket.idea`？

而家 `idea_to_ticket` 係正式 Python package。

所以 primary command 用：

```bash
python -m idea_to_ticket.idea
```

比直接 run file 更符合 package 用法，imports 亦更乾淨。

名稱可以同時係：

```text
GitHub repo:    ai-dev-workflow
Workflow name:  Idea-to-Ticket
Python package: idea_to_ticket
```

完全正常。

---

# 10. Stage 2A 完成時你會見到乜？

```text
pytest ✅
fixture CLI ✅
schema validation ✅
readable rendering ✅
CLI ✅
```

但真正輸入 idea 時，可能仍然見到：

```text
AI provider not configured
```

呢個係正常，因為 Stage 2B 仲未做。

---

# 11. Stage 2B 你要決定乜？

Harry 要 approve：

```text
AI Provider
AI Model
```

例如：

```text
Provider: OpenAI
Model: <chosen model>
```

Cursor 之後先加入嗰一個 provider 所需嘅 SDK / HTTP client，同埋 live provider class。

唔應該一開始加 OpenAI + Anthropic + xAI 全部。

---

# 12. API Key 點處理？

如果最後揀 OpenAI，local machine 可以有：

```env
OPENAI_API_KEY=...
```

真 key 放 local `.env` 或 environment variable。

唔可以放入：

```text
GitHub
README
.env.example
source code
```

`.env.example` 只保留空 placeholder：

```env
OPENAI_API_KEY=
```

---

# 13. 真正 live test 會係點？

Stage 2B 完成之後：

```bash
python -m idea_to_ticket.idea
```

例如輸入：

```text
I want to add a way to detect duplicate ideas.
```

真正流程：

```text
Raw Idea
   ↓
idea_intake.md prompt
   ↓
Approved AI Provider
   ↓
Structured JSON
   ↓
TicketDraft Validation
   ↓
Readable Ticket
```

呢次成功先叫 **Stage 2 COMPLETE**。

---

# 14. 點解 Stage 2 唔 create GitHub Issue？

核心原則：

```text
Idea captured
≠ GitHub Issue automatically created
≠ Coding automatically starts
```

Stage 2：

```text
Idea → Ticket Draft
```

Stage 3 先做：

```text
Ticket Draft
      ↓
Search GitHub
      ↓
Duplicate?
      ↓
Harry Approves?
      ↓
Create GitHub Issue
```

所以 Stage 2 唔接 GitHub 係刻意嘅 stage boundary。

---

# 15. `duplicate_of` 點解 Stage 2 一定要 null？

完整 ticket model 將來會有 `duplicate_of`。

但 Stage 2 冇 search GitHub，所以唔應該聲稱知道 duplicate。

因此：

```json
"duplicate_of": null
```

到 Stage 3 真正做 duplicate search 先加入 semantics。

---

# 16. `ready_for_development` 點解只係 display？

AI 可以建議：

```text
Ready for Development: Yes / No
```

但 AI 唔係最終決策者。

Harry 先係。

所以 Stage 2 唔會因為 AI 話 `true` 就：

```text
自動改 GitHub status
自動開始 coding
```

Human gate 必須保留。

---

# 17. Cursor 而家實際會做乜？

當你 approve plan 後，先做 Stage 2A：

```text
1. Rename idea-to-ticket → idea_to_ticket
2. Add pyproject.toml
3. Create package files
4. Define TicketDraft
5. Write schema tests
6. Write renderer + tests
7. Write pipeline + tests
8. Write FakeDrafter
9. Write CLI
10. Add examples
11. Run pytest
12. Run fixture CLI
```

Stage 2A checkpoint：

```text
STOP → Harry review
```

之後 Stage 2B：

```text
13. Harry chooses provider/model
14. Add only that provider dependency
15. Implement live provider
16. Configure API locally
17. Run a real idea
18. Verify validated readable ticket
19. Update docs
20. STOP
```

---

# 18. Harry 喺 Stage 2 嘅角色

你唔需要自己逐行手寫 `idea.py`。

你主要做：

```text
Approve architecture
Approve provider/model
Approve dependencies
Review Cursor changes
Run/observe tests
Try a real idea
Decide Stage 2 pass/fail
```

角色：

```text
Harry       = Owner / Final Authority
Cursor      = Implementation Agent
AI Provider = Ticket Writer at runtime
```

---

# 19. Cursor 同 Runtime AI 唔係同一個角色

## Cursor

Cursor 做：

```text
寫 Python code
寫 schema
寫 tests
寫 pipeline
```

即係：

> **build the machine**

## Runtime AI

例如 OpenAI 做：

```text
理解 Harry 嘅 raw idea
產生 ticket content
```

即係：

> **run inside the machine**

所以：

```text
Cursor → builds Idea-to-Ticket
AI provider → writes tickets through Idea-to-Ticket
```

---

# 20. 最後成功 Picture

```text
                 BUILD TIME

Harry
  ↓
Requirements / Approval
  ↓
Cursor
  ↓
Builds:
CLI + Schema + Pipeline + Renderer + Tests + Provider integration


                 RUN TIME

Harry
  ↓
Raw Idea
  ↓
Idea-to-Ticket CLI
  ↓
Approved AI Provider
  ↓
Structured Ticket JSON
  ↓
Pydantic Validation
  ↓
Readable Ticket
  ↓
Harry Reviews
```

---

# 21. Stage 2 Definition of Done — 人話版

全部做到先算完成：

```text
[ ] idea_to_ticket/ 係正常 Python package
[ ] pytest 全部 pass
[ ] sample ticket 可以 validate
[ ] 錯 ticket 會被 reject
[ ] fixture command 可以 output readable ticket
[ ] 只接一個真 AI provider
[ ] 真 API key 冇 commit
[ ] Harry 可以輸入一個真 idea
[ ] AI 真係產生 structured ticket
[ ] structured ticket validation pass
[ ] readable ticket 正常顯示
[ ] README / AGENTS / setup docs naming 更新
[ ] 冇 create GitHub Issue
[ ] 冇 GitHub Project automation
[ ] 冇開始 Stage 3
```

---

# 22. 我對而家 Implementation Plan 嘅評價

目前版本方向係合理而且足夠清楚：

```text
Stage 2A
Local Core

        ↓ checkpoint

Stage 2B
One Live AI Provider

        ↓ live manual verification

Stage 2 Complete
```

值得保留嘅設計：

1. Stage 2A 唔依賴 network / API key。
2. 真 AI integration 成功先算 Stage 2 complete。
3. Stage 2 只接一個 provider，避免 over-engineering。
4. Pydantic 做 validation gate。
5. Renderer 唔交畀 AI，令 output 穩定。
6. Human authority 保持喺 Harry。
7. Stage 2 完全唔碰 GitHub Issue automation。
8. Python package 用 `idea_to_ticket`，workflow 名仍然用 Idea-to-Ticket。

---

# 23. 我建議你下一步

而家唔需要再 redesign Stage 2。

下一步可以正式叫 Cursor：

```text
Implement Stage 2A only.
```

先唔接真 AI。

等 Stage 2A：

```text
pytest ✅
fixture CLI ✅
```

你 review 完，再揀一個 provider 做 Stage 2B。

---

## 最簡單記法

> **Stage 2A：先整好 ticket machine。**

> **Stage 2B：再將一個真 AI 插入部 machine。**

> **Stage 2 完成：你輸入 idea，AI 真係產生一張 validated ticket。**

> **Stage 3 先會將 approved ticket 變成 GitHub Issue。**
