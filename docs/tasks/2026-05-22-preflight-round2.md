# Preflight SKILL.md 第二轮修复

> 创建: 2026-05-22 | 来源: dry-run 测试发现 8 个问题 | 文件: `.claude/skills/preflight/SKILL.md`

---

## Task 1 (P1): 逃生门增加二次验证

**问题:** 用户只要说出任何听起来像"独特角度"的理由就通过逃生门，有决心的用户几乎总能绕过 early stop。

**位置:** Phase 1，Escape hatch 之后

**当前内容:**
```markdown
If YES → continue to Phase 2 despite competition. The user's unique angle may
find a gap the market scan missed. If NO → STOP and recommend USE EXISTING or PIVOT.
```

**改为:**
```markdown
If YES → run a 30-second sanity check before continuing: "Can I verify in one
search that this angle is NOT already covered by the competitors I found?"
Search once. If the angle IS already covered → report the finding and revert
to the STOP recommendation. If genuinely uncovered → continue to Phase 2.
If NO → STOP and recommend USE EXISTING or PIVOT.
```

---

## Task 2 (P2): 消除 Phase 1 和 Phase 2 的搜索重复

**问题:** Phase 1 搜出竞品列表，Phase 2 又要求重新收集 stars/commits/features——同一批项目的同一批信息被收集两次。

**位置:** Phase 1 末尾 + Phase 2 开头

**改为:**

Phase 1 末尾（在 Escape hatch 之后）增加：
```markdown
**If continuing to Phase 2:** carry forward the competitor list already found.
Note each project's stars, last commit date, and a one-line summary now —
do not re-search these in Phase 2.
```

Phase 2 开头改为：
```markdown
### Phase 2: Deep Analysis (10 min, only if Phase 1 is green/yellow)

**Start from the competitor list collected in Phase 1.** For each, expand:
- Contributor count and community health (issues, PR velocity)
- Feature coverage in detail (what does it do? what doesn't it do?)
- Community feedback (Reddit/HN discussions, common complaints in issues)
```

---

## Task 3 (P1): Own pain 不应完全跳过 Phase 4

**问题:** Exit gates 表中 `Own pain → Phase 5` 跳过了偏差检测，但 Own pain 场景恰恰是 confirmation bias 和 overconfidence 高发区。

**位置:** Workflow 开头 Phase exit gates 表

**当前:**
```markdown
| Phase 3 | Motivation = Own pain | → Phase 5 (bias scan still valuable, market scan less relevant) |
```

**改为:**
```markdown
| Phase 3 | Motivation = Own pain | → Phase 4, then Phase 5 (market scan less relevant, but bias detection is critical — own-pain builders are most susceptible to overconfidence and confirmation bias) |
```

---

## Task 4 (P1): pre-mortem 致命发现必须影响推荐

**问题:** Phase 5 让用户做 pre-mortem，发现致命问题后只有一个模糊的 "which of these can we verify NOW?"——没有规定如果验证确认了致命问题该怎么办。

**位置:** Phase 5，Run pre-mortem 之后

**当前:**
```markdown
Then check: which of these failure modes can we verify NOW?
```

**改为:**
```markdown
Then check: which of these failure modes can we verify NOW?

**If pre-mortem reveals a failure mode that is likely AND has no known mitigation:**
- Lower confidence to LOW regardless of other findings
- Explicitly flag this failure mode in the recommendation
- If the failure mode is structural (cannot be mitigated by better execution),
  this is grounds for ABANDON even if the market gap exists
```

---

## Task 5 (P2): Phase 3 和 Phase 6 之间增加数据衔接

**问题:** Phase 3 收集了动机、优势、成功定义、约束，但没有要求记录。Phase 6 的 archive 模板需要这些信息，agent 得回头翻对话。

**位置:** Phase 3 末尾（在 "Read project context" 之后）

**增加:**
```markdown
**Before moving on:** summarize findings here in 2-3 lines. These will be copied
into the Phase 6 decision document:
- Motivation: [learn / portfolio / own-pain / community / commercial]
- Unique advantage: [one line]
- Success definition: [one line]
- Constraints: [one line]
```

---

## Task 6 (P1): Phase 7 handoff 机制具体化

**问题:** "Let brainstorming/writing-plans/coding skills take over" 没有定义 agent 该做什么具体动作。

**位置:** Phase 7，BUILD 分支

**当前:**
```markdown
- 🟢 BUILD → Archive decision. Report: "Preflight complete. Handing off to implementation."
  **Exit preflight mode entirely.** Let brainstorming/writing-plans/coding skills take over.
  Do NOT constrain or re-evaluate during implementation — that's not your role.
```

**改为:**
```markdown
- 🟢 BUILD → Archive decision. Then:
  1. Verbally report: "Preflight complete. Recommendation: BUILD (confidence: [level])."
  2. Summarize the key decision context in 2-3 lines for the next skill
  3. Invoke the brainstorming skill with: "User wants to build [project]. Preflight
     context: [motivation], [key gap found], [main risk from pre-mortem]."
  4. Exit preflight mode — do NOT constrain or re-evaluate during implementation.
```

---

## Task 7 (P2): 搜索工具不可用时的回退方案

**问题:** Edge Cases 只有一句 "flag low confidence"，没有给出具体回退步骤。实际中 `pip search` 已被禁用，GitHub 可能触发 rate limit。

**位置:** Edge Cases 部分，"Research tools are unavailable"

**当前:**
```markdown
**Research tools are unavailable:**
Note the limitation, do what you can with available tools, flag "low confidence" in the output.
```

**改为:**
```markdown
**Research tools are unavailable:**
If WebSearch/WebFetch are blocked or rate-limited, fall back in this order:
1. Use the Grep/Glob tools to check local cache or previously downloaded data
2. Search from the model's training knowledge (note: "based on training data, may be outdated")
3. Ask the user: "I can't search right now. Do you already know of similar tools? List any you're aware of."
Flag "low confidence" in the output and set decision expiry to 1 month regardless of domain.

Specific tool notes:
- `pip search` is disabled by PyPI — use `pip install <name> --dry-run` or web search instead
- GitHub search may rate-limit — spread searches across different query terms
- If ALL search tools fail → ask user whether to proceed with training-knowledge-only scan or defer
```

---

## Task 8 (P2): Own pain + 无竞品场景快速通道

**问题:** 即使用户动机是 Own pain 且 Phase 1 没有发现竞品，仍要跑 Phase 2→4→5→6→7。对"我就是想解决自己的问题"的场景太慢。

**位置:** Phase exit gates 表

**增加一行:**
```markdown
| Phase 3 | Motivation = Own pain AND Phase 1 found 0 competitors | → Recommend BUILD, archive lightweight decision (proposal + motivation + 2-line scan result), exit. Skip Phase 4-7. |
```

---

## 执行顺序

```
Task 1 (P1) → Task 3 (P1) → Task 4 (P1) → Task 6 (P1)
    ↓
Task 2 (P2) + Task 5 (P2) + Task 7 (P2) + Task 8 (P2)  // 可并行
```

全部完成后通知审查终端验证。
