# Preflight SKILL.md 第四轮修复

> 创建: 2026-05-22 | 来源: 15 案例测试 + 功能性评估 | 文件: `skills/preflight/SKILL.md`

---

## Task 1 (P1): Phase 1 增加 Learn → BUILD 快速通道

**问题:** Learn 项目在 Phase 1 skip early stop 后，还要经过 Phase 2（10 分钟深度竞品分析），然后 Phase 3 立刻触发 `Learn → BUILD`。10 分钟纯浪费。

**位置:** Phase 1 末尾（在 "carry forward the competitor list" 之前）

**增加段落到 Phase 1 的 Step 1 之后:**

```markdown
**Step 1b — Learn fast track:**
If motivation = 🎓 Learn, skip Phase 2-5 entirely. Do a one-line competitor note
("X, Y, Z exist, fyi"), recommend BUILD, write a 4-line archive (proposal +
motivation + 1-line scan), and exit. Learning justifies building regardless of
the competitive landscape.
```

**同时更新 exit gates 表，在 Phase 1 行中增加:**

```markdown
| Phase 1 | 3+ mature competitors AND motivation = Learn | → Recommend BUILD, archive lightweight decision, exit (learning justifies building — no need for deep analysis) |
```

当前 exit gates 表中 Learn/Own-pain 合并在同一行（line 121），需要拆开为两行。

---

## Task 2 (P1): 实现 gap → requirements 映射（或从 README 移除声称）

**问题:** README 比较表声称 preflight 做 "Gap → requirement mapping" 和 "Writes requirements"。实际上 Phase 2 识别了 gaps 但没有转化为任何 requirements。要么实现，要么诚实。

**两个选项（让用户选）:**

### Option A: 实现（推荐，claim 本身有价值）

在 Phase 2 末尾增加：

```markdown
**Gap → Requirement translation:**
For each 🔴 gap in the coverage matrix, write one concrete requirement:

| Gap | Requirement | Priority |
|-----|-------------|----------|
| No offline-first support | Must sync data locally before cloud, resolve conflicts CRDT-style | P0 (differentiator) |
| No CLI interface | Must expose all core functions via CLI, not just GUI | P1 (addresses developer users) |

Requirements should be specific enough that someone could start coding from them.
```

在 Phase 6 archive 模板中增加：

```markdown
## Requirements from Gaps
| Gap | Requirement | Priority |
|-----|-------------|----------|
| ... | ...         | ...      |
```

### Option B: 移除声称

修改 README.md 比较表，将 "Gap → requirement mapping" 和 "Writes requirements" 两行改为：
- `Identifies competitive gaps` 
- `Gap-informed differentiation strategy`

修改 description frontmatter 去掉 "feature" 一词（当前第一句暗示 feature-level scope）。

---

## Task 3 (P2): Q1 区分"草稿原型"和"已有成熟项目"

**问题:** Q1 判断 "Does NOT yet exist as code in the current repo" — 如果用户花了一个周末写了个草稿原型，Q1 会把它当作"已有项目"而跳过 preflight。但此时恰恰需要竞争验证——沉没成本正在积累。

**位置:** Decision Tree 的 Q1

**改为:**

```
Q1: Is this a NEW standalone initiative?
│   Does NOT yet exist as MATURE code in the current repo.
│   NOT a significant modification, extension, or repair of
│   an established codebase.
│
│   ⚠️ Weekend prototypes and abandoned drafts (< 1 week of work,
│   no users, not deployed) count as "new" — escalate to preflight.
│   The sunk cost of a prototype is small; the sunk cost of building
│   something that already exists is much larger.
│
├── NO (mature project already exists) → STOP.
│
└── YES → continue to Q2
```

---

## Task 4 (P3): 竞品成熟度评估从二元改为三维

**问题:** 当前 "active commits in last 3 months, >1000 stars, or backed by a known organization" 满足任一条件就算成熟。三个维度完全不同量级，不应同等对待。

**位置:** Phase 1 的 EARLY STOP CONDITION 定义

**改为:**

```markdown
**EARLY STOP CONDITION:** Count mature competitors using the scoring below.
A project is "mature" if it scores ≥ 2 points:

| Signal | Points | Notes |
|--------|--------|-------|
| Active maintenance (commits in last 3 months) | 2 | Primary signal of viability |
| >1000 GitHub stars | 1 | Popularity, not quality |
| Backed by known organization | 1 | Sustained funding |
| >100 open issues with no response | -1 | Zombie project — subtract |
| Last release > 1 year ago | -1 | Abandoned — subtract |

3+ projects scoring ≥ 2 → consider early stop.
```

---

## Task 5 (P3): Phase 1 Step 1 Portfolio 行消除歧义

**问题:** Phase 1 Step 1 说 Portfolio "raise the threshold to 5+ before stopping"，暗示提高阈值后仍有停止机制（可能是逃生门）。但 Step 2 明确写 "for Community/Commercial only"，Portfolio 在 5+ 时直接 ABANDON/PIVOT 无逃生门。

**位置:** Phase 1 Step 1 的 Portfolio 行

**当前:**
```markdown
- 📁 **Portfolio** → raise the threshold to 5+ mature competitors before stopping.
  Portfolio projects benefit from existing competition as comparison points.
```

**改为:**
```markdown
- 📁 **Portfolio** → raise the threshold to 5+ mature competitors before stopping.
  At 5+ mature examples, a portfolio piece won't differentiate — no escape hatch.
  Pick a less crowded space or angle for the portfolio to shine.
```

---

## 执行顺序

```
Task 1 (P1) → Task 2 (P1)  // Option A or B, user chooses
Task 3 (P2) + Task 4 (P3) + Task 5 (P3)  // 可并行
```

全部完成后跑 `docs/tasks/2026-05-22-preflight-round4-testcases.md` 中的 15 个案例验证。
