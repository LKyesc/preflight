# Preflight — Design Spec

## Summary
A Claude Code skill that enforces research-before-building. When a user proposes a new project/feature/tool, preflight searches for existing solutions, analyzes the competitive landscape, evaluates user motivation and constraints, detects cognitive biases, runs a pre-mortem analysis, and generates a recommendation (build/pivot/abandon/use-existing). All decisions are archived for future reference.

## Problem
AI coding agents default to "build first, think later." This leads to:
1. Building tools that already have 10+ mature competitors
2. Investing days before discovering structural flaws
3. Switching directions repeatedly with no memory of why previous ideas failed

## Differentiation
Existing skills are split into two disconnected categories:
- Market research skills (for founders: "should I start a business?")
- Requirements skills (for engineers: "what features should I build?")

Preflight is neither. It is a **decision-quality enforcement layer** that connects competitive analysis to requirements definition, weighted by the user's actual motivation and context.

Unique capabilities not found in any existing skill:
1. Connects competitor gaps → differentiation requirements
2. Detects 8 cognitive biases including "context blindness"
3. Pre-mortem failure analysis before any code is written
4. User motivation-weighted recommendations (learning vs. commercial)
5. Persistent decision archives with expiration dates
6. Explicitly recommends "don't build" as a first-class outcome

## Architecture
A single SKILL.md file with 7 phases:
1. **Rapid Landscape Scan** — parallel search across GitHub, web, skill registries, package managers. Early stop if 3+ mature competitors found.
2. **Deep Analysis** — competitor coverage matrix, gap identification
3. **User Context & Motivation** — determine WHY (learn/portfolio/own-pain/community/commercial), unique advantage, success definition, constraints
4. **Cognitive Bias Detection** — 8 bias types with algorithmic detection and debiasing actions
5. **Decision Synthesis** — pre-mortem + opportunity cost + motivation-weighted recommendation
6. **Archive** — structured decision document with expiration tag
7. **Exit** — handoff to implementation skills or archive; preflight does not linger

## Key Design Decisions
- **Motivation-weighted recommendation**: Market saturation doesn't matter for learning projects
- **Checkpoint, not chaperone**: Preflight exits after decision; no mid-implementation interference
- **Early stop**: 80% of ideas should abort at Phase 1
- **Decision legacy**: Archives prevent re-litigating abandoned ideas

## Future Extensions
- Plugin marketplace listing for one-command install
- Integration with brainstorming/writing-plans for automatic handoff
- Decision re-evaluation reminders based on expiration tags
