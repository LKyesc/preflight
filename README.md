# Preflight

> *"The single most valuable outcome of preflight is 'don't build this.'"*

A Claude Code skill that enforces research-before-building. Before you write any code for a new project, preflight searches for existing solutions, analyzes competitors, detects cognitive biases, and archives the decision — so you don't waste weeks building what already exists.

## The Problem

AI coding agents default to "build first, think later." This means:

- You build a tool → discover 10 competitors already exist
- You invest 3 days → realize there's a structural flaw no amount of code can fix  
- You switch directions 4 times → each time from scratch, no memory of why the last idea failed

**Preflight inserts 15 minutes of intentional friction before hours of wasted coding.**

## Install

```bash
# Add to your project
mkdir -p .claude/skills/preflight
cp SKILL.md .claude/skills/preflight/

# Or install via plugin marketplace (coming soon)
claude plugin marketplace add <your-org>/preflight
```

## How It Works

```
User: "Let's build a tool that does X"
    ↓
Phase 1: Rapid Scan (5 min)
    Search GitHub, web, skill registries, package managers in parallel
    ↓
    Found 3+ mature competitors? → STOP. "Use existing or pivot."
    ↓
Phase 2: Deep Analysis (10 min)
    Build coverage matrix, identify differentiation gaps
    ↓
Phase 3: Bias Check
    Detect anchoring, overconfidence, novelty bias, sunk cost...
    ↓
Phase 4: Decision Synthesis
    Pre-mortem + opportunity cost → 🟢Build / 🟡Pivot / 🔴Abandon / 📋Use Existing
    ↓
Phase 5: Archive
    Write structured decision doc to docs/decisions/
    ↓
Phase 6: Handoff
    If BUILD → transfer to brainstorming skill for design
    If ABANDON → decision recorded, never re-litigate
```

## What Makes It Different

| | Market Research Skills | Requirements Skills | **Preflight** |
|---|---|---|---|
| Searches competitors | ✅ | ❌ | ✅ |
| Writes requirements | ❌ | ✅ | ✅ |
| **Connects gaps → requirements** | ❌ | ❌ | ✅ |
| **Detects cognitive biases** | ❌ | ❌ | ✅ |
| **Pre-mortem analysis** | ❌ | ❌ | ✅ |
| **Recommends "don't build"** | ❌ | ❌ | ✅ |
| **Archives decisions for reuse** | ❌ | ❌ | ✅ |

Preflight isn't a market research tool or a requirements tool. It's a **decision-quality enforcement layer** for AI agents — combining behavioral economics, competitive intelligence, and requirements analysis into one workflow.

## Example Decision Output

See `docs/decisions/` for real examples. Each decision document captures:

- What was proposed and why
- What already exists (competitor matrix)
- What gaps were found
- Pre-mortem failure analysis
- Cognitive biases detected
- What's being sacrificed (opportunity cost)
- Recommendation with confidence level
- Expiration date for re-evaluation

## Philosophy

After 15 years of "eliminate all friction," 2026 is seeing the return of **intentional friction** — deliberate pauses at high-risk moments. Preflight is intentional friction for AI-assisted development.

- **Good friction:** selective, explained, applied when the cost of being wrong is high
- **Bad friction:** ubiquitous, unexplained, blocking everything

Preflight is the good kind. It doesn't slow you down — it prevents you from sprinting in the wrong direction.

## Background

Built from real experience: three consecutive project ideas (MCP package manager, PR triage tool, skill recommendation CLI) were started before discovering they were either market-saturated or structurally flawed. Each pivot required re-learning what was already learned. Preflight makes this process explicit, fast, and archived.
