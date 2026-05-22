# Preflight

> *"The single most valuable outcome of preflight is 'don't build this.'"*

**Decision quality enforcement for AI agents.** Before you write any code for a new project, preflight searches for existing solutions, analyzes competitors, detects cognitive biases, runs a pre-mortem, and archives the decision — so you don't waste weeks building what already exists.

## Install

### Via Claude Code Plugin Marketplace

```bash
claude plugin marketplace add preflight-skills/preflight
claude plugin install preflight@preflight
```

### Manual Install

```bash
# Per-project
cp skills/preflight/SKILL.md .claude/skills/preflight/SKILL.md

# Global (all projects)
cp skills/preflight/SKILL.md ~/.claude/skills/preflight/SKILL.md
```

## How It Works

7 phases with early-exit gates:

```
User: "Let's build X"
    ↓
Phase 1: Rapid Scan (5 min) — parallel search GitHub, web, registries, packages
    ├→ 3+ mature competitors + no escape hatch → STOP
    ↓
Phase 2: Deep Analysis — competitor coverage matrix, gap identification
    ├→ True greenfield → skip to Phase 5
    ↓
Phase 3: User Context — motivation (learn/portfolio/own-pain/community/commercial)
    ├→ Learn → BUILD, exit
    ├→ Own pain → skip to Phase 5
    ↓
Phase 4: Cognitive Bias Detection — 8 bias types with concrete debiasing
    ├→ >3 high-risk biases → flag, lower confidence
    ↓
Phase 5: Decision Synthesis — pre-mortem + opportunity cost + motivation-weighted recommendation
    ├→ 🟢 BUILD / 🟡 PIVOT / 🔴 ABANDON / 📋 USE EXISTING
    ↓
Phase 6: Archive — structured decision doc with expiration date
    ↓
Phase 7: Exit — handoff to implementation or archive. Preflight job done.
```

## What Makes It Different

| | Market Research | Requirements | **Preflight** |
|---|---|---|---|
| Searches competitors | ✅ | ❌ | ✅ |
| Writes requirements | ❌ | ✅ | ✅ |
| **Gap → requirement mapping** | ❌ | ❌ | ✅ |
| **User motivation weighting** | ❌ | ❌ | ✅ |
| **Cognitive bias detection (8 types)** | ❌ | ❌ | ✅ |
| **Pre-mortem analysis** | ❌ | ❌ | ✅ |
| **Recommends "don't build"** | ❌ | ❌ | ✅ |
| **Decision archive + expiry** | ❌ | ❌ | ✅ |

Preflight is not a market research tool or a requirements tool. It's a **decision-quality enforcement layer** — combining behavioral economics, competitive intelligence, and requirements analysis into one workflow.

## Example Output

See `docs/decisions/` for 3 real examples (MCP package manager, PR triage tool, skill recommendation CLI — all abandoned after preflight analysis). Each archived decision captures:

- Proposal summary
- Landscape scan results
- Competitor coverage matrix
- Pre-mortem failure analysis
- Cognitive biases detected
- Opportunity cost
- Recommendation with confidence level
- Expiration date

## Philosophy

After 15 years of "eliminate all friction," 2026 is seeing the return of **intentional friction** — deliberate, selective pauses at high-risk moments. Preflight is intentional friction for AI-assisted development.

- **Good friction:** selective, explained, applied when the cost of being wrong is high
- **Bad friction:** ubiquitous, unexplained, blocking everything

Preflight is the good kind. It doesn't slow you down — it prevents you from sprinting in the wrong direction.

## Background

Built from real experience: three consecutive project ideas (MCP package manager, PR triage tool, skill recommendation CLI) were started before discovering they were either market-saturated or structurally flawed. Each pivot required re-learning what was already learned. Preflight makes this process explicit, fast, and archived.

## References

- Kahneman, D. (2011). *Thinking, Fast and Slow*
- Klein, G. (2007). *The Power of Intuition*
- CloudServ (2026). ["The Return of Intentional Friction"](https://cloudserv.ai/the-return-of-intentional-friction-why-some-2026-systems-are-designed-to-slow-you-down/)
- Gartner (2026). ["Critical Capabilities for Decision Intelligence Platforms"](https://www.gartner.com/en/documents/7367030)
