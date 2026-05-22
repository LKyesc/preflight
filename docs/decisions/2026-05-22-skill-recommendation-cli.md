---
decision: abandon
confidence: high
expires: 2026-07-22
biases_detected: [novelty, confirmation, anchoring]
related: ["2026-05-22-mcp-package-manager.md", "2026-05-22-pr-triage-tool.md"]
---

# Skill Recommendation CLI

## Proposal
Build a CLI that asks 3-5 preference questions and recommends Claude Code skills
based on user's development context.

## Landscape Scan Results
Searched: GitHub "skill recommendation claude code", web "skill discovery tool",
claude-plugins.dev, skills.sh.

**Found 10+ active competitors:**

| Project | Approach | Key Capability |
|---------|----------|---------------|
| superskillret | Embedding retrieval from 16,783 skills | Semantic matching, 0.3s CPU |
| ctx | Knowledge graph of 1,450+ skills | Real-time project-aware recommendations |
| SkillIssue | Cross-marketplace search | 52 plugins, 3 marketplaces |
| skill-fetch | Multi-registry (9 sources) | Quality scoring 0-100, security labels |
| skills-manager | Cross-platform GUI/CLI | 12 IDE support, one-click install |
| CCPM | CLI registry | search, install, update, popular, recent |
| skills-installer | Multi-client installer | Interactive search, claude-plugins.dev |
| @biggora/claude-plugins | Full CLI marketplace | Discovery, install, management |
| skillz-rs | Rust package manager | GitHub-based skill install |
| agentic-rig | Codebase analysis | Config generation + skill discovery |
| Dispatch | Proactive runtime interception | Mid-session tool recommendations |

**Registries:** SkillsMP (800K+), skills.sh (91K+), claude-plugins.dev (63K+ skills, 12K plugins)

**Official catalogs:** Anthropic (86.7K stars), OpenAI (12.8K), Vercel (8.9K), HuggingFace (8.5K)

## Competitor Coverage Matrix

| Capability | superskillret | ctx | skill-fetch | CCPM | Ours? |
|-----------|--------------|-----|-------------|------|-------|
| Semantic search | ✅ (embeddings) | ✅ (KG) | ❌ | ❌ | ❌ |
| Real-time recommendation | ❌ | ✅ | ❌ | ❌ | ❌ |
| Multi-registry search | ❌ | ❌ | ✅ (9 sources) | ❌ | ❌ |
| Security scanning | ❌ | ❌ | ✅ | ❌ | ❌ |
| Install management | ❌ | ❌ | ❌ | ✅ | ❌ |
| Preference-based recommendation | ❌ | ❌ | ❌ | ❌ | ✅ |

Only differentiation: "preference-based recommendation." But this is a thin feature, not a
product. superskillret and ctx already do better matching (embeddings and knowledge graphs
vs. our rule-based matching).

## Pre-mortem
If this failed 6 months from now:
- **Cause 1:** "It's just a preference form + CSV lookup" — too simple to sustain interest
- **Cause 2:** superskillret/ctx already provide better recommendations automatically
- **Cause 3:** The skill registry landscape consolidates, making discovery trivial

## Cognitive Biases
- **Novelty**: Third hot idea in a row, pattern of chasing "what's trending"
- **Confirmation**: Focused on "nobody does preference-based matching" while ignoring that
  embedding-based matching is strictly better
- **Anchoring**: Skill ecosystem looked like the "obvious" next play after MCP tools

## Opportunity Cost
Building this sacrificed: starting preflight skill, which is the meta-solution to this
exact problem (jumping between ideas without researching first).

## Recommendation
**🔴 ABANDON.** The skill discovery space is extremely saturated. Our proposed
differentiation (preference-based recommendation) is strictly inferior to existing
embedding and knowledge-graph approaches. Every major AI company already has an
official skill catalog.

## Next Steps
- Archive this decision
- If preflight had existed at the start of this process, we would have saved 4+ hours
  of discussion and ideation across all three abandoned projects
- Use this as the motivating example for why preflight itself is valuable
