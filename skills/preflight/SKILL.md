---
name: preflight
description: >
  Mandatory pre-build checklist. Before writing ANY code for a new project
  or tool: search existing solutions, analyze competitors, find
  differentiation gaps, detect cognitive biases, and archive the decision.
  If you skip this, you will waste time building what already exists.
  Invoke when user proposes "build X", "create a tool", "make a new project",
  "develop a skill", "let's do Y from scratch".
  Do NOT invoke for bug fixes, minor edits, feature additions, or existing-project tasks.
---

# Preflight — Decision Quality Enforcement for AI Agents

## Philosophy

AI agents default to "build first, think later" — preflight is the intentional
friction that prevents wasted work. **The most valuable outcome is "don't build
this."** 80% of new ideas already have viable solutions.

**Preflight is a checkpoint, not a chaperone.** Once the user decides to build,
exit completely. Analyze, recommend, archive, then get out of the way.

Inspired by: pre-flight checklists (aviation), pre-mortem analysis (Klein),
cognitive bias research (Kahneman).

## When to Invoke

Preflight uses **intent-based triggering**, not keyword matching. Before invoking, evaluate
the user's request against the three criteria below, in order. All three must be satisfied
for full preflight.

### Decision Tree (evaluate in order, stop at first NO)

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

Q2: What is the SCALE of the new thing?
│
├── Trivial: one function, a config change, a single <50-line script
│   → STOP. Do NOT invoke.
│
├── Small: multi-file utility, quick one-off CLI, < 4 hours work
│   → LIGHTWEIGHT preflight: Phase 1 scan only, no archive.
│     Just answer: "does this already exist?" and report.
│
└── Medium+: new package, tool, service, skill, library, app
    → Continue to Q3 for FULL preflight.

Q3: Is the user COMMITTED or just exploring?
│
├── Exploring: "what if we...", "could we...", "有没有可能...",
│   "是不是可以...", thinking out loud with no clear decision
│   → Ask: "Is this a serious proposal or thinking out loud?"
│     If serious → proceed. If just exploring → skip preflight.
│
├── Already researched: user mentions specific competitors,
│   says "I've looked at X and Y, they don't do Z"
│   → LIGHTWEIGHT: Phase 1 + Phase 3 (motivation), skip deep scan.
│
└── Committed: "let's build", "I want to create", "做一个新的...",
    user is in an empty directory, or names a project that doesn't
    exist yet → FULL preflight.
```

### Quick Disambiguation

**Hard stop** — NEVER trigger, regardless of phrasing: bug fixes, refactoring, tests,
docs, feature additions to existing projects, single-function/file requests,
"how do I...", "explain...", "show me...", or Chinese patterns like
"帮我查/看/解释/找/改/修/优化一下..." and "这个代码/这个bug/这个报错...".

If uncertain after the decision tree, ask exactly ONE question:
"Is this a new standalone project, or part of the current codebase?" Then decide.

## Workflow

**Phase exit gates — when to skip ahead:**

| Phase | Condition | Skip to |
|-------|-----------|---------|
| Phase 1 | 3+ mature competitors AND motivation = Community/Commercial AND escape hatch fails | → Recommend USE EXISTING or PIVOT, archive, exit |
| Phase 1 | 5+ mature competitors AND motivation = Portfolio | → Recommend USE EXISTING or PIVOT, archive, exit (Portfolio threshold is 5+, not 3+ — existing competition can serve as comparison points) |
| Phase 1 | 3+ mature competitors AND motivation = Learn | → Recommend BUILD, archive lightweight decision, exit (learning justifies building — no need for deep analysis) |
| Phase 1 | 3+ mature competitors AND motivation = Own-pain | → Skip early stop, continue to Phase 2 (building to solve own problem is valid regardless of competition) |
| Phase 1 | 0 competitors found (true greenfield) | → Skip Phase 2 (nothing to deep-dive), go directly to Phase 3 |
| Phase 3 | Motivation = Learn | → Recommend BUILD, archive, exit (learning justifies building regardless) |
| Phase 3 | Motivation = Own pain AND Phase 1 found >0 competitors | → Phase 4 (focus on overconfidence + confirmation bias), then Phase 5 |
| Phase 3 | Motivation = Own pain AND Phase 1 found 0 competitors | → Quick bias check (overconfidence + confirmation only, 2 min), then recommend BUILD, archive lightweight decision, exit |
| Phase 4 | >3 high-risk biases detected | → Flag explicitly in recommendation, lower confidence to "low" |
| Any | User overrides and demands BUILD | → Archive the override reason, exit. Preflight advises, user decides. |

### Lightweight Mode

Triggered when Q2 determines SCALE = Small (< 4 hours work). Run ONLY:

1. Search for existing solutions (same 4 sources as Phase 1)
2. Quick motivation check: learning? own pain? other?
3. Weighted by motivation:
   - Learn/Own-pain + competitors exist → "X, Y, Z exist, but you're building to
     learn/solve your own problem — go ahead. Skim them for ideas though."
   - Other + 3+ mature → "X, Y, Z already cover this. Worth your time?"
   - 0-2 competitors → "Nothing matching found. Safe to proceed."
4. Do NOT archive, no bias detection, no pre-mortem — stop after the report.

---

### Phase 1: Rapid Landscape Scan (5 min)

Goal: find out if this already exists.

Search in parallel: GitHub (`site:github.com <keywords>`), web (`<idea> open source tool library`), skill registries (claude-plugins.dev, skills.sh), package managers (PyPI, npm, cargo).

**EARLY STOP CONDITION:** Count mature competitors using the scoring below.
A project is "mature" if it scores ≥ 2 points:

| Signal | Points | Notes |
|--------|--------|-------|
| Active maintenance (commits in last 3 months) | 2 | Primary signal of viability |
| >1000 GitHub stars | 1 | Scale to domain: for niche tools, >100 stars in that ecosystem can be equivalent |
| Backed by known organization | 1 | Sustained funding |
| >50 open issues AND last commit > 6 months ago | -1 | Combined zombie indicator — only applies when maintenance is already absent |
| Last release > 2 years ago AND last commit > 1 year ago | -1 | Long-term inactivity — ignore for stable projects with recent commits |

3+ projects scoring ≥ 2 → consider early stop. BUT first check motivation, then escape hatch:

**Step 1 — Motivation check (do this BEFORE the escape hatch):**
If the user's motivation is not already clear from conversation, determine it now.
- 🎓 **Learn** or 🔧 **Own-pain** → skip early stop entirely, continue to Phase 2.
  Building to learn or solve your own problem is valid regardless of competition.
  The motivation-weighted routing in Phase 3 will handle these cases properly.
- 📁 **Portfolio** → raise the threshold to 5+ mature competitors before stopping.
  At 5+ mature examples, a portfolio piece won't differentiate — no escape hatch.
  Pick a less crowded space or angle for the portfolio to shine.
- 🌍 **Community** or 💰 **Commercial** → proceed to escape hatch below.

**Step 1b — Learn fast track:**
If motivation = 🎓 Learn, skip Phase 2-5 entirely. Do a one-line competitor note
("X, Y, Z exist, fyi"), recommend BUILD, write a 4-line archive (proposal +
motivation + 1-line scan), and exit. Learning justifies building regardless of
the competitive landscape.

**Step 2 — Escape hatch (for Community/Commercial only):**
> "You have unique domain expertise, a specific pain that existing tools don't
> address, or a fundamentally different approach — any of these apply?"

If YES → run a 60-second sanity check before continuing: "Can I verify that this
angle is NOT already covered by the competitors I found?" Search twice with
different query angles (e.g., one feature-focused, one use-case-focused).
If the angle IS already covered → report the finding and revert to the STOP
recommendation. If genuinely uncovered → continue to Phase 2.
If one search finds coverage → treat as covered (conservative; false negatives
on coverage are cheaper than false positives).
If NO → STOP and recommend USE EXISTING or PIVOT.

**If continuing to Phase 2:** carry forward the competitor list already found.
Note each project's stars, last commit date, and a one-line summary now —
do not re-search these in Phase 2.

### Phase 2: Deep Analysis (10 min, only if Phase 1 is green/yellow)

**Start from the competitor list collected in Phase 1.** For each, expand:
- Contributor count and community health (issues, PR velocity)
- Feature coverage in detail (what does it do? what doesn't it do?)
- Community feedback (Reddit/HN discussions, common complaints in issues)

Build a **coverage matrix**:

```
| Feature/Dimension | Project A | Project B | Project C | Gap? |
|-------------------|-----------|-----------|-----------|------|
| Dimension 1       | ✅        | ❌        | ✅        |      |
| Dimension 2       | ❌        | ✅        | ❌        | ⚠️   |
| Dimension 3       | ❌        | ❌        | ❌        | 🔴   |
```

Red gaps = potential differentiation space.

**If no competitors found (greenfield):** note this explicitly. Greenfield is rare —
double-check search terms and scope before concluding.

**Gap → Requirement translation:**
For each 🔴 gap in the coverage matrix, write one concrete requirement:

| Gap | Requirement | Priority |
|-----|-------------|----------|
| No offline-first support | Must sync data locally before cloud, resolve conflicts CRDT-style | P0 (differentiator) |
| No CLI interface | Must expose all core functions via CLI, not just GUI | P1 (addresses developer users) |

Requirements should be specific enough that someone could start coding from them.

### Phase 3: User Context & Motivation Analysis

**This phase is equally important as the market scan.** Market data without user context
produces false negatives — it kills every idea because almost everything has been tried.
The user's motivation and background determine whether market saturation actually matters.

**Determine the user's WHY.** If not already clear from conversation, ask:

1. **Motivation:** 🎓 Learn / 📁 Portfolio / 🔧 Own-pain / 🌍 Community / 💰 Commercial
2. **Unique advantage:** domain expertise? existing audience? unique data? specific pain? none (honest)?
3. **Success definition:** "I learned how X works" / "it solves my problem" / "10 people use it" / "got a job interview" — what does "done" look like?
4. **Constraints:** time, skills, stack, solo/team, no ML? no paid APIs?

**How motivation changes the recommendation:**

| Motivation | Market saturated? | Recommendation |
|-----------|------------------|----------------|
| 🎓 Learn | Doesn't matter | 🟢 BUILD — learning happens through building |
| 📁 Portfolio | Matters somewhat | 🟢 BUILD if you can complete it well |
| 🔧 Own pain | Matters little | 🟢 BUILD — your use case IS the market |
| 🌍 Community | Matters a lot | 🟡 PIVOT if no gap, 🟢 if gap exists |
| 💰 Commercial | Matters completely | 🔴 ABANDON if saturated, 🟢 if real gap |

**Read project context:**
- Check CLAUDE.md for user preferences, constraints, tech stack
- Check existing `docs/decisions/` for related past decisions
- Note environment limitations (OS, available tools, no dataset, no ML infra, etc.)

**Before moving on:** summarize findings here in 2-3 lines. These will be copied
into the Phase 6 decision document:
- Motivation: [learn / portfolio / own-pain / community / commercial]
- Unique advantage: [one line]
- Success definition: [one line]
- Constraints: [one line]

### Phase 4: Cognitive Bias Detection

**Run cognitive bias detection.** Check for these 8 bias types:

| Bias | Detection | Concrete Debiasing Action |
|------|-----------|--------------------------|
| **Anchoring** | First idea dominates all discussion | Force-list 3 completely unrelated solution categories — not just variants of the first idea |
| **Confirmation** | Only searching for evidence that supports building | Assign "devil's advocate" role: write 3 specific attacks on the proposal, each citing concrete evidence |
| **Overconfidence** | Underestimating competition, overestimating differentiation | Find 5 similar GitHub repos. How many stopped maintenance within 2 years? Apply that base rate. |
| **Novelty** | New idea → automatically good | Search for similar proposals from 2 years ago. If any existed — why did they fail? |
| **Sunk cost** | "We've already discussed this so much" | Hard reset: "If you heard this idea for the first time 5 minutes ago, which direction would you pick?" Write the answer. |
| **Framing** | "This will get stars" driving decisions | Score separately: Quality (1-10) and Virality (1-10). Each score requires independent evidence. |
| **Halo** | One good aspect makes everything seem good | List at least as many weaknesses as strengths. Every weakness must cite a specific risk or data point. |
| **Context blindness** | Judging idea without considering WHY user is building | If motivation = Learn → skip Phase 2/3/4 entirely, go directly to BUILD recommendation |

### Phase 5: Decision Synthesis

**Run pre-mortem:**
> "Assume we built this and 6 months later it completely failed. Write the
> post-mortem. What killed it? Be specific — not 'nobody used it' but 'only
> 3 users tried it and none came back because...'"

Then check: which of these failure modes can we verify NOW?

**If pre-mortem reveals a failure mode that is likely AND has no known mitigation:**
- Lower confidence to LOW regardless of other findings
- Explicitly flag this failure mode in the recommendation
- If the failure mode is structural (cannot be mitigated by better execution),
  this is grounds for ABANDON even if the market gap exists

**Surface opportunity cost:**
> "If you commit to building this (estimated 1-4 weeks), what are you NOT doing?"
> List 2-3 specific alternatives being sacrificed.

**Weigh market reality against user motivation:**
The final recommendation must balance BOTH dimensions. Examples:
- "Market saturated with 10+ tools, BUT you're building to learn Go → 🟢 BUILD"
- "Greenfield AND you have deep domain expertise → 🟢 BUILD with high confidence"
- "Market saturated AND you want GitHub stars → 🔴 ABANDON or 🟡 PIVOT to the one open gap"
- "Greenfield BUT you have no domain knowledge or unique advantage → 🟡 BUILD cautiously, bring in expertise"

**Generate recommendation:**
- 🟢 **BUILD** — Clear gap, matches constraints, pre-mortem survivable
- 🟡 **PIVOT** — Gap exists but current angle won't work; try this other angle instead
- 🔴 **ABANDON** — Market saturated, structural flaw, or doesn't match constraints
- 📋 **USE EXISTING** — A mature project already solves this; here's how to use it

### Phase 6: Archive

Write decision document to `docs/decisions/YYYY-MM-DD-<slug>.md` using this format:

```markdown
---
decision: [build | pivot | abandon | use-existing]
confidence: [high | medium | low]
expires: YYYY-MM-DD
motivation: [learn | portfolio | own-pain | community | commercial]
biases_detected: [list]
related: [links to related decision files]
---

# [Topic]

## User Context
- Motivation: [why building this]
- Unique advantage: [what user brings]
- Success definition: [what "done" looks like]
- Constraints: [time, skills, resources]

## Proposal
[1-2 sentence summary of what was proposed]

## Landscape Scan Results
[What was searched, what was found]

## Competitor Coverage Matrix
[Table from Phase 2]

## Requirements from Gaps
| Gap | Requirement | Priority |
|-----|-------------|----------|
| ... | ...         | ...      |

## Pre-mortem
[Failure analysis]

## Cognitive Biases
[Bias types detected + debiasing actions taken]

## Opportunity Cost
[What's being sacrificed]

## Recommendation
[Decision + evidence + weighted by motivation]
[Example: "Market saturated, but you're building to learn → BUILD"]
[Example: "Clear gap + your domain expertise → BUILD with high confidence"]

## Next Steps
[Concrete actions based on recommendation]
```

**Set expiration:** Different domains have different decision half-lives:
- AI tools / MCP ecosystem: re-evaluate after **1-2 months**
- Web frameworks / libraries: re-evaluate after **6 months**
- General programming: re-evaluate after **1 year**

### Phase 7: Exit — Preflight Is a Checkpoint, Not a Chaperone

**Once the user makes a decision, preflight's job is DONE.** Do not second-guess.
Do not re-run the analysis. Do not say "are you sure?" The user owns the decision.

Based on recommendation:
- 🟢 BUILD → Archive decision. Then:
  1. Report: "Preflight complete. Recommendation: BUILD (confidence: [level])."
  2. Hand off to the next design/implementation skill with a structured context block:
     ```
     Project: [name]
     Motivation: [learn / portfolio / own-pain / community / commercial]
     Key gap: [primary differentiation gap from Phase 2 matrix]
     Main risk: [top pre-mortem finding]
     First requirement: [one concrete requirement from gap→requirement mapping]
     ```
  3. Exit preflight mode — do NOT constrain or re-evaluate during implementation.
- 🟡 PIVOT → Present the pivot suggestion. If user wants to explore it, re-run preflight
  as a fresh cycle on the new angle. If they don't, exit.
- 🔴 ABANDON → Archive decision. If the topic resurfaces later, reference this decision
  rather than re-researching from scratch.
- 📋 USE EXISTING → Present installation/usage guide for the recommended tool. Exit.

## Scope Boundary

Preflight is a **decision checkpoint**, not a continuous monitor.

- **YES:** One-time scan + bias detection + recommendation before building; re-run on major pivots
- **NO:** Continuous monitoring, periodic re-evaluation (use expiry dates), mid-sprint check-ins

## Success Metrics

Preflight's value is the bad decisions it prevents, not the builds it enables:

- **Time saved:** Hours NOT spent building what already exists
- **Pivots triggered:** Ideas redirected by competitive findings
- **Abandons with evidence:** Stopping decisions backed by data, not hunches
- **Bias catches:** Debiasing that changed a BUILD recommendation

If preflight never recommends ABANDON or PIVOT, it's not being applied broadly enough.

## Anti-Patterns

**NEVER:** skip Phase 1 ("I already know"), present only BUILD (user excitement),
let sunk cost drive, omit the pre-mortem, use preflight as gatekeeping,
linger after the decision, or re-analyze during implementation.

**ALWAYS:** archive every decision (especially ABANDON), present evidence,
separate facts from opinions, note confidence level.

## Edge Cases

**Brainstorming out loud:** Q3 handles this. If exploring ("what if...", "could we..."),
ask once if serious. If not, skip.

**Research tools unavailable:** Fall back: 1) Grep/Glob for local cache, 2) model's
training knowledge (flag as "may be outdated"), 3) ask user what they already know.
Always flag "low confidence" and set expiry to 1 month. Specific notes:
`pip search` is disabled — use web search; GitHub may rate-limit — spread queries;
if ALL tools fail → ask user to proceed with training-knowledge-only scan or defer.

**User disagrees:** User has final say. Document the override. Preflight is advisory.

**User skips preflight:** Remind once: "We might build something that already exists."
If insistent, document a "skipped preflight" entry.

**Decision changes mid-research:** Normal. Update analysis as you learn.

## References

- Kahneman, D. (2011). *Thinking, Fast and Slow* — cognitive biases framework
- Klein, G. (2007). *The Power of Intuition* — pre-mortem methodology
