# Preflight SKILL.md 修复任务清单

> 创建日期: 2026-05-22
> 来源: 全方位审查 preflight SKILL.md 内容合理性
> 目标终端: 执行终端（另一个 Claude Code 实例）
> 审查终端: 当前终端（负责最终 review + 评估）

---

## 任务总览

共 8 个任务：**1 个 P0（事实错误）、4 个 P1（设计缺陷）、3 个 P2（增强优化）**

执行顺序：P0 → P1（可并行） → P2（可并行）

---

## P0 — 必须修复

### Task 1: 修复 Phase 编号不一致

**文件:** `.claude/skills/preflight/SKILL.md`

**问题:**
- Phase 1-5 使用了 `### Phase X:` 格式
- Phase 5: Decision Synthesis 之后应该是 Phase 6: Archive、Phase 7: Exit
- 但实际写的是 `### Phase 5: Archive` 然后 `### Phase 6: Exit`
- 目录中 Workflow 有 7 个步骤，正文章节标题只有 6 个 Phase

**修改内容:**
1. 将 `### Phase 5: Archive` 改为 `### Phase 6: Archive`
2. 将 `### Phase 6: Exit` 改为 `### Phase 7: Exit — Preflight Is a Checkpoint, Not a Chaperone`
3. 将原来的 `### Phase 5: Decision Synthesis` 保持为 Phase 5
4. 全文通读一遍确保所有交叉引用一致

**验收标准:** `grep "^### Phase" SKILL.md` 输出 Phase 1 到 Phase 7，且编号连续无跳空。

---

## P1 — 应该修复

### Task 2: 标注未验证的参考文献

**文件:** `.claude/skills/preflight/SKILL.md` (References 部分)

**问题:**
```markdown
- CloudServ (2026). "The Return of Intentional Friction" — good vs. bad friction
- Gartner (2026). "Decision Intelligence Critical Capabilities" — decision governance predictions
```
这两个引用在 2026-05-22 时点可能不存在。如果是博客文章/预印本/虚构引用，应如实标注。

**修改内容:**
1. 搜索验证 "CloudServ The Return of Intentional Friction 2026" 是否真实存在
2. 搜索验证 "Gartner Decision Intelligence Critical Capabilities 2026" 是否真实存在
3. 如果不存在或来源非正式：
   - 移除虚构引用
   - 或改为标注来源类型，如 `来源: CloudServ 博客 (2026)` 或 `概念来源: Gartner 决策智能报告系列`
4. 如果确实存在：补充 URL 或 DOI

**验收标准:** References 部分每一条引用都可验证或已标注来源类型。

---

### Task 3: 调整 EARLY STOP CONDITION 的阈值和逻辑

**文件:** `.claude/skills/preflight/SKILL.md` (Phase 1 部分)

**问题:**
> "If 3+ mature projects (active commits, >100 stars) already do substantially the same thing → STOP"

- 100⭐ 门槛对某些领域太低（AI 工具领域 100⭐ 很常见）
- "substantially the same thing" 标准模糊
- 可能过早扼杀有差异化空间的项目

**修改内容:**
在 EARLY STOP 后增加一个快速逃生门：

```markdown
**EARLY STOP CONDITION:** If 3+ mature projects (active commits in last 3 months,
>500 stars, or backed by a known organization) already do substantially the same
thing → consider stopping. BUT first check for an escape hatch:

**Escape hatch — ask ONE question before stopping:**
> "Does the user have unique domain expertise, a specific pain point that existing
> tools don't address, or a fundamentally different interaction paradigm?"

If YES → continue to Phase 2 despite competition. If NO → STOP and recommend USE EXISTING.
```

**验收标准:**
- Phase 1 包含逃生门逻辑
- 门槛从 100⭐ 提升到 500⭐ 或有组织背书
- "active commits" 加了时间限定 (3 months)

---

### Task 4: 增强认知偏差检测的 debiasing actions

**文件:** `.claude/skills/preflight/SKILL.md` (Phase 4 部分)

**问题:**
当前 debiasing actions 太多 "Actively search" / "Explicitly search"——在 AI agent 手里这些都是同一种操作（搜索更多信息）。真正有效的 debiasing 需要结构性对抗。

**修改内容:**
在 Phase 4 的偏差表格中增加一列 "Concrete Debiasing Action"，针对每种偏差给出可执行动作：

| Bias | 当前 Action | 改进为 |
|------|-----------|--------|
| Anchoring | Actively search for alternatives | 强制列举 3 个完全不相关的解决方案类别（不只是同一类别的不同实现） |
| Confirmation | Explicitly search for reasons NOT to build | 分配"反对者角色"：用 3 句话攻击这个提案，每句话必须引用具体证据 |
| Overconfidence | Apply "outside view" | 查找 5 个类似提案的 GitHub 仓库，统计其中有多少在 2 年内停止维护 |
| Novelty | Ask "why hasn't anyone done it?" | 搜索 2 年前是否有类似提案，如果有——当时为什么失败了？ |
| Sunk cost | Reset perspective | 假设你昨晚才听说这个想法，你现在会选哪个方向？写出具体选择 |
| Framing | Separate "is this good?" from "will this get attention?" | 分别打分：质量分 (1-10) 和 传播分 (1-10)，两列必须独立论证 |
| Halo | List strengths AND weaknesses | 至少列出同等数量的缺点和优点，每条缺点附具体证据 |
| Context blindness | Cross-reference motivation | 如果动机是 Learn → 跳过 Phase 2/3/4，直接 BUILD |

**验收标准:** Phase 4 中每种偏差都有具体可执行动作，不再只有 "search more"。

---

### Task 5: 增加"非全新项目"的触发分流

**文件:** `.claude/skills/preflight/SKILL.md` (When to Invoke 部分)

**问题:**
当前 trigger 只覆盖"我想做一个新项目"。大量实际场景是：
- "在现有项目里加一个功能"
- "做 A 的过程中发现可能需要 B"
- "这个想法已经反复想了 3 天了"

**修改内容:**
在 "When to Invoke" 和 "Do NOT invoke for" 之间增加一个分流逻辑：

```markdown
**Gray area — ask for clarification when:**

| Scenario | Question to ask |
|----------|----------------|
| "Add X feature to existing project" | "Is X a core direction change or just an incremental addition?" |
| "I've been thinking about this for days" | "Want me to run a quick scan to validate before we commit more time?" |
| "This emerged while building Y" | "Is this a dependency of Y or a separate idea that can wait?" |
| "I need a tool that does X" | "New project or integrating into an existing workflow?" |

**For incremental features in existing projects:** run a lightweight version —
Phase 1 scan only, no full Phase 2-7. Archive as `docs/decisions/features/`.
```

**验收标准:**
- When to Invoke 部分包含 Gray area 分流表
- 明确了轻量级流程（仅 Phase 1）用于增量功能

---

## P2 — 优化增强

### Task 6: 定义每个 Phase 的提前退出条件

**文件:** `.claude/skills/preflight/SKILL.md` (Workflow 部分)

**问题:**
当前只有 Phase 1 有 EARLY STOP，但实际上：
- Phase 3 发现动机是 🎓Learn → 可以直接 BUILD，不需要 Phase 4-5
- Phase 2 发现真正的 greenfield（零竞品） → 可以跳过 Phase 4 的部分步骤
- Phase 4 检测到多个高风险偏差 → 应该增加审查深度而非继续推进

**修改内容:**
在每个 Phase 开头增加 "Exit gate"：

```markdown
**Phase exit gates:**

| Phase | Condition | Skip to |
|-------|-----------|---------|
| Phase 1 | 3+ mature competitors AND no escape hatch | → Recommendation: USE EXISTING |
| Phase 2 | True greenfield (0 competitors found) | → Phase 5 (skip bias deep-dive) |
| Phase 3 | Motivation = Learn | → Recommendation: BUILD (decision archived, Phase 4-5 optional) |
| Phase 3 | Motivation = Own pain | → Phase 5 (bias scan still valuable) |
| Phase 4 | >3 high-risk biases detected | → Flag explicitly in recommendation, lower confidence |
```

**验收标准:** 每个 Phase 开头有明确的 Exit gate 规则。

---

### Task 7: 扩充中文触发词

**文件:** `.claude/skills/preflight/SKILL.md` (When to Invoke 部分)

**问题:**
当前中文触发词太少："我想做一个/开发一个/构建一个..."
缺少常见的口语化表达。

**修改内容:**
```markdown
中文触发词扩展：
- "咱们能不能搞一个..."
- "写一个工具来..."
- "要不要搭一个..."
- "我需要一个能..."
- "能不能帮我做一个..."
- "有没有什么工具可以..." → 如果用户想的是"没有就自己做一个"
- "我想试试做..."
- "我们来做..."
```

**验收标准:** 中文触发词覆盖至少 8 种常见表达。

---

### Task 8: 明确 preflight 的"一次性 vs 持续性"架构边界

**文件:** `.claude/skills/preflight/SKILL.md` (Phase 7 + Philosophy 部分)

**问题:**
当前哲学是 "preflight is a checkpoint, not a chaperone"（一次性检查点）。
但 PM skill 讨论中暴露了"执行阶段持续决策审查"的需求。
这两个定位有冲突，需要在文档中明确。

**修改内容:**
在 Philosophy 或 Phase 7 部分增加一个明确声明：

```markdown
## Scope Boundary

Preflight is a **decision checkpoint**, not a **continuous monitor**. Specifically:

- **YES:** One-time competitive scan + bias detection + recommendation before building
- **YES:** Re-running preflight when a major pivot is proposed mid-project
- **NO:** Continuous monitoring during active development
- **NO:** Periodic re-evaluation of past decisions (use decision expiry dates instead)
- **NO:** Mid-sprint check-ins, progress tracking, or task management

If you need continuous decision quality enforcement during execution (e.g., "are we
still on track?", "has a new competitor emerged?"), that is a separate concern that
may warrant its own skill — it is explicitly NOT preflight's responsibility. This
boundary keeps preflight focused and prevents scope creep into project management.
```

**验收标准:**
- Philosophy 或 Phase 7 部分包含明确的 Scope Boundary
- 清晰区分了一次性检查和持续监控的边界

---

## 执行说明

### 给执行终端的指令

```
请按以下顺序执行 preflight SKILL.md 的修复任务：

1. 先执行 P0-Task1（修复编号），完成后 commit
2. P1-Task2/3/4/5 可并行执行，每完成一个 commit
3. P2-Task6/7/8 可并行执行，每完成一个 commit
4. 全部完成后，通知审查终端进行 review

文件路径: F:/LLM document/preflight/.claude/skills/preflight/SKILL.md
决策参考: F:/LLM document/preflight/docs/decisions/2026-05-22-pm-skill.md
```

### 给审查终端的指令（当前终端）

审查终端负责：
1. 检查每个 task 的执行结果是否符合验收标准
2. 运行 `grep "^### Phase" SKILL.md` 验证编号
3. 检查变更是否引入新的不一致
4. 最终确认后给出通过/需返工的评估

---

## 任务状态

| Task | 优先级 | 状态 | 执行者 | 审查结果 |
|------|--------|------|--------|----------|
| Task 1: 修复编号 | P0 | ⬜ 待执行 | 执行终端 | - |
| Task 2: 标注引用 | P1 | ⬜ 待执行 | 执行终端 | - |
| Task 3: Early stop | P1 | ⬜ 待执行 | 执行终端 | - |
| Task 4: 偏差检测 | P1 | ⬜ 待执行 | 执行终端 | - |
| Task 5: 触发分流 | P1 | ⬜ 待执行 | 执行终端 | - |
| Task 6: Exit gates | P2 | ⬜ 待执行 | 执行终端 | - |
| Task 7: 中文触发词 | P2 | ⬜ 待执行 | 执行终端 | - |
| Task 8: Scope边界 | P2 | ⬜ 待执行 | 执行终端 | - |
