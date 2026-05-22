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

Speed without direction is chaos. AI agents default to "build first, think later" —
this skill is the intentional friction that prevents wasted work.

**The single most valuable outcome of preflight is "don't build this."**
80% of new project ideas already have viable existing solutions. Finding them
in 5 minutes saves weeks of pointless development.

**Preflight is a checkpoint, not a chaperone.** Once the user decides to build,
preflight exits completely. It does not constrain implementation, re-evaluate
mid-build, or second-guess decisions. Do the analysis, deliver the recommendation,
archive the decision, then get out of the way.

Inspired by: pre-flight checklists (aviation), pre-mortem analysis (Gary Klein),
intentional friction (CloudServ 2026), cognitive bias research (Kahneman).

## When to Invoke

Preflight uses **intent-based triggering**, not keyword matching. Before invoking, evaluate
the user's request against the three criteria below, in order. All three must be satisfied
for full preflight.

### Decision Tree (evaluate in order, stop at first NO)

```
Q1: Is this a NEW standalone initiative?
│   Does NOT yet exist as code in the current repo.
│   NOT a modification, extension, or repair of existing code.
│
├── NO → STOP. Do NOT invoke preflight.
│         (bug fix, refactor, add feature to existing project,
│          modify existing file, "how do I...", "explain this...")
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

### Context Signals (weigh these, don't keyword-match)

**Signals that preflight SHOULD trigger:**
- User proposes a named project/tool that doesn't exist in the current repo
- User is in an empty or freshly-initialized directory
- The request would create a new top-level directory structure
- User mentions "publish", "release", "open source", "launch"

**Signals that preflight should NOT trigger:**
- Request references files or directories that already exist in the repo
- Request contains "in this project", "add to", "update the", "fix the"
- Task is completable in < 10 targeted edits to existing files
- User says "how do I...", "can you explain...", "check/look at..."
- Chinese patterns that are NOT new-project proposals:
  "帮我查/看/解释/找/改/修/优化一下..." (look up, check, explain, find, fix, repair, optimize)
  "这个代码/这个bug/这个报错..." (this code/bug/error)

### Explicit Skip List

These NEVER trigger preflight, regardless of phrasing:
- Bug fixes, error resolution, debugging
- Refactoring, style changes, code cleanup
- Tests, documentation, comments
- Feature additions to existing projects (even large ones — these are extensions, not new projects)
- Single-function or single-file requests
- "How do I...", "Explain...", "Show me...", "What is..."

### One-Question Rule

If still uncertain after the decision tree, ask exactly ONE question:
- "Is this a new standalone project, or part of the current codebase?"

Do not ask a second question. Make the call based on the answer.

## Workflow

**Phase exit gates — when to skip ahead:**

| Phase | Condition | Skip to |
|-------|-----------|---------|
| Phase 1 | 3+ mature competitors AND motivation = Community/Commercial AND escape hatch fails | → Recommend USE EXISTING or PIVOT, archive, exit |
| Phase 1 | 5+ mature competitors AND motivation = Portfolio | → Recommend USE EXISTING or PIVOT, archive, exit (Portfolio threshold is 5+, not 3+ — existing competition can serve as comparison points) |
| Phase 1 | 3+ mature competitors AND motivation = Learn or Own-pain | → Skip early stop, continue to Phase 2 (building to learn or solve own problem is valid regardless of competition) |
| Phase 1 | 0 competitors found (true greenfield) | → Skip Phase 2 (nothing to deep-dive), go directly to Phase 3 |
| Phase 3 | Motivation = Learn | → Recommend BUILD, archive, exit (learning justifies building regardless) |
| Phase 3 | Motivation = Own pain AND Phase 1 found >0 competitors | → Phase 4 (focus on overconfidence + confirmation bias), then Phase 5 |
| Phase 3 | Motivation = Own pain AND Phase 1 found 0 competitors | → Quick bias check (overconfidence + confirmation only, 2 min), then recommend BUILD, archive lightweight decision, exit |
| Phase 4 | >3 high-risk biases detected | → Flag explicitly in recommendation, lower confidence to "low" |
| Any | User overrides and demands BUILD | → Archive the override reason, exit. Preflight advises, user decides. |

### Lightweight Mode

Triggered when Q2 determines SCALE = Small (multi-file utility, quick CLI, < 4 hours).
Run ONLY the following, then exit:

1. Search for existing solutions (same 4 sources as Phase 1)
2. Quick motivation check — ask or infer: learning? own pain? other?
3. Report results, weighted by motivation:
   - Learn or Own-pain + competitors exist → "X, Y, Z exist, but you're building to
     learn/solve your own problem — go ahead. Worth skimming them for ideas though."
   - Other + 3+ mature competitors → "X, Y, Z already cover this well. Worth your time?"
   - 0-2 competitors → "Nothing matching found. Looks safe to proceed."
4. Do NOT archive a decision document
5. Do NOT run bias detection, pre-mortem, or any Phase 2-6 steps

---

### Phase 1: Rapid Landscape Scan (5 min)

Goal: find out if this already exists.

Search in parallel:
1. **GitHub** — `site:github.com <keywords>` for repos with similar functionality
2. **Web** — `<idea description> open source tool library` for blog posts, comparisons
3. **Skill registries** — claude-plugins.dev, skills.sh for similar Claude Code skills
4. **Package managers** — PyPI (`pip search`), npm, cargo for similarly-named packages

**EARLY STOP CONDITION:** If 3+ mature projects (active commits in last 3 months,
>1000 stars, or backed by a known organization) already do substantially the same
thing → consider stopping. BUT first check motivation, then escape hatch:

**Step 1 — Motivation check (do this BEFORE the escape hatch):**
If the user's motivation is not already clear from conversation, determine it now.
- 🎓 **Learn** or 🔧 **Own-pain** → skip early stop entirely, continue to Phase 2.
  Building to learn or solve your own problem is valid regardless of competition.
  The motivation-weighted routing in Phase 3 will handle these cases properly.
- 📁 **Portfolio** → raise the threshold to 5+ mature competitors before stopping.
  Portfolio projects benefit from existing competition as comparison points.
- 🌍 **Community** or 💰 **Commercial** → proceed to escape hatch below.

**Step 2 — Escape hatch (for Community/Commercial only):**
> "You have unique domain expertise, a specific pain that existing tools don't
> address, or a fundamentally different approach — any of these apply?"

If YES → run a 30-second sanity check before continuing: "Can I verify in one
search that this angle is NOT already covered by the competitors I found?"
Search once. If the angle IS already covered → report the finding and revert
to the STOP recommendation. If genuinely uncovered → continue to Phase 2.
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

### Phase 3: User Context & Motivation Analysis

**This phase is equally important as the market scan.** Market data without user context
produces false negatives — it kills every idea because almost everything has been tried.
The user's motivation and background determine whether market saturation actually matters.

**Determine the user's WHY.** If not already clear from conversation, ask:

1. **Motivation:** Why build this?
   - 🎓 **Learn** — building for education, understanding how something works
   - 📁 **Portfolio** — demonstrating skills to employers/clients
   - 🔧 **Solve own pain** — scratching a personal itch, success = it works for you
   - 🌍 **Community contribution** — filling a gap for others
   - 💰 **Commercial** — aiming for users/adoption/revenue

2. **Unique advantage:** What do you bring that competitors don't have?
   - Domain expertise (e.g., you're a PM building PM tools)
   - Existing audience or distribution channel
   - Unique data or access
   - Specific pain that existing tools don't address for YOUR use case
   - None (building a pure commodity) — this is honest and useful to know

3. **Success definition:** What does "this worked" look like?
   - "I learned how X works" → success, regardless of users
   - "It solves my own problem" → success, regardless of stars
   - "10 people use it" → different bar than "1000 stars on GitHub"
   - "It got me a job interview" → success, regardless of adoption

4. **Constraints:** What are the real limitations?
   - Time available (hours per week, total runway)
   - Technical skills and stack preferences
   - No ML infrastructure, no datasets, no paid APIs?
   - Solo developer or team?

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
  1. Verbally report: "Preflight complete. Recommendation: BUILD (confidence: [level])."
  2. Summarize the key decision context in 2-3 lines for the next skill
  3. Invoke the brainstorming skill with: "User wants to build [project]. Preflight
     context: [motivation], [key gap found], [main risk from pre-mortem]."
  4. Exit preflight mode — do NOT constrain or re-evaluate during implementation.
- 🟡 PIVOT → Present the pivot suggestion. If user wants to explore it, re-run preflight
  as a fresh cycle on the new angle. If they don't, exit.
- 🔴 ABANDON → Archive decision. If the topic resurfaces later, reference this decision
  rather than re-researching from scratch.
- 📋 USE EXISTING → Present installation/usage guide for the recommended tool. Exit.

## Scope Boundary

Preflight is a **decision checkpoint**, not a **continuous monitor**. Specifically:

- **YES:** One-time competitive scan + bias detection + recommendation before building
- **YES:** Re-running preflight when a major pivot is proposed mid-project
- **NO:** Continuous monitoring during active development
- **NO:** Periodic re-evaluation of past decisions (use decision expiry dates to trigger re-runs)
- **NO:** Mid-sprint check-ins, progress tracking, or task management

If you need continuous decision quality enforcement during execution (e.g., "are we
still on track?", "has a new competitor emerged since we started?"), that is a separate
concern that may warrant its own skill — it is explicitly NOT preflight's responsibility.
This boundary keeps preflight focused and prevents scope creep into project management.

## Anti-Patterns

**NEVER:**
- Skip Phase 1 because "I already know the answer" — always verify
- Present only the "build" recommendation because the user seems excited
- Let sunk cost ("we already discussed this") influence the assessment
- Omit the pre-mortem — it's the most powerful part of the process
- Use preflight as a gatekeeping excuse — "don't build" is advice, not authority
- Linger after the decision — once user decides BUILD, exit and don't look back
- Re-analyze during implementation — preflight's job ends at the handoff

**ALWAYS:**
- Archive every decision, even (especially) abandoned ones
- Present evidence for every conclusion
- Separate facts from opinions in the analysis
- Note your own confidence level — uncertainty is honest

## Edge Cases

**User is just brainstorming out loud:**
Already handled by Q3 in the decision tree. If the user is exploring ("what if...", "could we..."),
ask once whether it's serious. If not, skip preflight entirely.

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

**User disagrees with the recommendation:**
The user always has the final say. Document their override reason in the decision archive.
Preflight is advisory, not authority.

**User wants to skip preflight:**
Remind them once: "Skipping preflight means we might build something that already exists.
Are you sure?" If yes, document the skip in `docs/decisions/` as a "skipped preflight" entry.

**Decision changes mid-research:**
This is normal and good. Update the analysis as you learn. The process is iterative, not linear.

## References

- Kahneman, D. (2011). *Thinking, Fast and Slow* — cognitive biases framework
- Klein, G. (2007). *The Power of Intuition* — pre-mortem methodology
- CloudServ (2026). ["The Return of Intentional Friction"](https://cloudserv.ai/the-return-of-intentional-friction-why-some-2026-systems-are-designed-to-slow-you-down/) — good vs. bad friction in 2026 system design
- Gartner (2026). ["Critical Capabilities for Decision Intelligence Platforms"](https://www.gartner.com/en/documents/7367030) (Doc ID: 7367030, Jan 27, 2026) — predicts 50% of business decisions will be augmented by AI agents by 2027
