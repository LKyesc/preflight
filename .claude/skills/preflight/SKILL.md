---
name: preflight
description: >
  Mandatory pre-build checklist. Before writing ANY code for a new project,
  feature, or tool: search existing solutions, analyze competitors, find
  differentiation gaps, detect cognitive biases, and archive the decision.
  If you skip this, you will waste time building what already exists.
  Invoke when user proposes "build X", "create a tool", "make a new project",
  "develop a skill", "let's do Y from scratch".
  Do NOT invoke for bug fixes, minor edits, or existing-project tasks.
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

**Trigger phrases** (user says anything resembling these):
- "我想做一个/开发一个/构建一个..." (I want to build/create/make...)
- "Let's build/create/make/develop a new..."
- "我们来做一个..." (Let's make a...)
- "要不要试试做..." (Want to try building...)
- "能不能开发一个..." (Can you develop a...)
- "I have an idea for a project/tool/skill..."

**Do NOT invoke for:**
- Bug fixes in existing code
- Adding features to an existing project (unless it's a major new direction)
- Refactoring, testing, documentation updates
- Questions about how something works
- "帮我查一下/看一下这个" (Help me look up / check something)

**If unsure:** ask "Is this a new project idea? Should I run preflight on it?"

## Workflow

### Phase 1: Rapid Landscape Scan (5 min)

Goal: find out if this already exists.

Search in parallel:
1. **GitHub** — `site:github.com <keywords>` for repos with similar functionality
2. **Web** — `<idea description> open source tool library` for blog posts, comparisons
3. **Skill registries** — claude-plugins.dev, skills.sh for similar Claude Code skills
4. **Package managers** — PyPI (`pip search`), npm, cargo for similarly-named packages

**EARLY STOP CONDITION:** If 3+ mature projects (active commits, >100 stars) already do
substantially the same thing → STOP. Report findings immediately. Recommend:
- "Use existing" if they fully meet the need
- "Pivot" if a different angle might still work

### Phase 2: Deep Analysis (10 min, only if Phase 1 is green/yellow)

For each competitor found, collect:
- Stars, last commit date, contributor count → health signal
- Feature coverage (what does it do? what doesn't it do?)
- Community feedback (issues, Reddit/HN discussions)

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

### Phase 4: Cognitive Bias Detection

**Run cognitive bias detection.** Check for these 7 bias types:

| Bias | Detection | Debiasing Action |
|------|-----------|-----------------|
| **Anchoring** | First idea dominates all discussion | Actively search for alternatives to the first idea |
| **Confirmation** | Only searching for evidence that supports building | Explicitly search for reasons NOT to build |
| **Overconfidence** | Underestimating competition, overestimating differentiation | Apply "outside view": what % of similar projects succeed? |
| **Novelty** | New idea → automatically good | Ask: "if this idea is so good, why hasn't anyone done it?" |
| **Sunk cost** | "We've already discussed this so much" | Reset: "if we learned about this idea 5 minutes ago, would we start?" |
| **Framing** | "This will get stars" driving decisions | Separate: "is this good?" from "will this get attention?" |
| **Halo** | One good aspect makes everything seem good | List BOTH strengths and weaknesses explicitly |
| **Context blindness** | Judging idea without considering WHY user is building | Always cross-reference motivation before final recommendation |

### Phase 5: Decision Synthesis

**Run pre-mortem:**
> "Assume we built this and 6 months later it completely failed. Write the
> post-mortem. What killed it? Be specific — not 'nobody used it' but 'only
> 3 users tried it and none came back because...'"

Then check: which of these failure modes can we verify NOW?

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
- 🟢 BUILD → Archive decision. Report: "Preflight complete. Handing off to implementation."
  **Exit preflight mode entirely.** Let brainstorming/writing-plans/coding skills take over.
  Do NOT constrain or re-evaluate during implementation — that's not your role.
- 🟡 PIVOT → Present the pivot suggestion. If user wants to explore it, re-run preflight
  as a fresh cycle on the new angle. If they don't, exit.
- 🔴 ABANDON → Archive decision. If the topic resurfaces later, reference this decision
  rather than re-researching from scratch.
- 📋 USE EXISTING → Present installation/usage guide for the recommended tool. Exit.

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
Ask: "Is this a serious proposal or just thinking out loud? I can do a quick scan or a full preflight."

**Research tools are unavailable:**
Note the limitation, do what you can with available tools, flag "low confidence" in the output.

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
- CloudServ (2026). "The Return of Intentional Friction" — good vs. bad friction
- Gartner (2026). "Decision Intelligence Critical Capabilities" — decision governance predictions
