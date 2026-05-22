# 第四轮修复后验证案例

> 创建: 2026-05-22 | 在修复 Task 1-5 之后运行

## 新增测试案例

### Test A: Learn + 饱和市场 → 快速通道
> "Build a Redis clone in Rust to learn about networking and memory management"

| Phase 1 搜索 | 6+ 竞品 |
| Step 1 动机 | 🎓 Learn |
| **修复后预期** | Phase 1 直接识别 Learn，不经过 Phase 2-5，4 行归档，🟢 BUILD，退出 |
| **修复前** | 经过 Phase 2 深度分析后才在 Phase 3 触发 BUILD（浪费 Phase 2） |

### Test B: 周末原型 → 不应被 Q1 误判
> "I built a prototype last weekend — a CLI that syncs notes between devices. Should I continue?"

| **修复前 Q1** | 代码已在 repo → STOP，不触发 preflight |
| **修复后 Q1** | 周末原型 < 1 周 → 算新项目 → 继续 Q2 |
| **预期** | FULL preflight 正常执行，在 Phase 1 发现 Syncthing, Resilio 等竞品，做出合理推荐 |

### Test C: 已有成熟项目 → 正确不触发
> "Add dark mode toggle to my production blog (500 commits, deployed, has users)"

| Q1 | 成熟项目，有用户 → STOP |
| **预期** | 不触发 preflight ✅ |

### Test D: 竞品成熟度三维评分
> "Build a GPU-accelerated CSV parser"

| Phase 1 | 找到一个项目: 800 stars, 6 个月无提交, 50+ open issues 无回复 |
| **修复前评分** | stars < 1000, 无提交 > 3 月, 无组织背书 → 不算"成熟" |
| **修复后评分** | stars(0), 维护(0), 组织(0), issues 无回复(-1) = -1 分 → < 2, 不算成熟 |
| **预期** | 不触发 early stop，进入 Phase 2 |

### Test E: Portfolio 行消除歧义
> "Build a React component library (6+ competitors) for my portfolio"

| Phase 1 Step 1 | 📁 Portfolio → 阈值 5+ → 6 ≥ 5 |
| **修复后文案** | 明确说 "no escape hatch. Pick a less crowded space." |
| **预期** | 🔴 ABANDON/PIVOT — 用户清楚知道为什么没有逃生门 |

---

## 15 个核心案例回归测试

修复后必须重跑以下全部，确保没有退化：

### 完整模式
1. **Learn + 6 竞品** → 🟢 BUILD（现在走 Phase 1 快速通道）
2. **Own-pain + 3 竞品 + gap** → 🟢 BUILD
3. **Own-pain + 0 竞品** → 🟢 BUILD fast
4. **Commercial + gap + 专长** → 🟢 BUILD
5. **Commercial + 10 竞品 + 无角度** → 🔴 ABANDON
6. **Portfolio + 4 竞品** → 🟢 BUILD
7. **Portfolio + 6 竞品** → 🔴 ABANDON
8. **Community + 0 竞品 + gap** → 🟢 BUILD
9. **Commercial + 5 竞品 + 有效逃生门** → 🟢 BUILD
10. **用户强制覆盖** → 🟢 BUILD

### 轻量模式
11. **Small + Learn + 竞品存在** → 🟢 放行
12. **Small + Commercial + 3+ 竞品** → ⚠️ 提醒
13. **Small + 0 竞品** → 🟢 放行

### 决策树
14. **已有成熟项目功能追加** → 不触发
15. **Trivial 规模** → 不触发
