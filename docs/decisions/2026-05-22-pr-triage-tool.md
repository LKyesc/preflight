---
decision: abandon
confidence: high
expires: 2026-08-22
biases_detected: [sunk-cost, overconfidence, novelty]
related: ["2026-05-22-mcp-package-manager.md"]
---

# PR-Triage: AI-Generated PR Detector

## Proposal
Build `pr-triage` — a CLI tool and GitHub Action that detects AI-generated pull requests
using heuristic rules (text patterns, code patterns, metadata signals). Scored 0-100,
categorized as LOW/MEDIUM/HIGH risk. MVP built with 13 passing tests.

## Landscape Scan Results
No direct competitor found (AI PR detection specifically). But adjacent issue well-documented:
- curl shut down its 6-year bug bounty due to AI-generated fake reports
- GitHub introduced "disable all PRs" setting
- Voiceflow estimates only 1 in 10 AI-generated PRs is legitimate

## Pre-mortem — THE CRITICAL FINDING
If this tool became popular and was open-sourced:
**The detection rules become training data for the adversary.**

Attack chain:
1. Attacker reads our open-source rules (text patterns, code patterns, thresholds)
2. Uses pr-triage as a discriminator to train a generator model
3. Generator outputs PRs that score LOW on all our detectors
4. Detection effectiveness drops to near-zero

This is a GAN-like adversarial loop with no known defense for open-source heuristic systems.
Output obfuscation (removing exact scores), repo-specific fingerprinting, and perturbation
layers can increase attacker cost but cannot solve the fundamental problem.

## Cognitive Biases
- **Sunk cost**: MVP was already built with 13 passing tests before this analysis was done
- **Overconfidence**: Assumed heuristic rules would be hard to bypass
- **Novelty**: "Nobody has done this exact thing" clouded the adversarial analysis

## Opportunity Cost
Building this sacrificed: starting preflight skill earlier, exploring prompt versioning tools.

## Recommendation
**🔴 ABANDON.** Structural adversarial vulnerability makes this fundamentally unsuitable
as an open-source project. A closed-source service might work but is outside scope/interest.

## Next Steps
- Archive this decision
- Re-evaluate only if a novel defense against GAN-style adversarial attacks on heuristic
  detection systems emerges (unlikely in near term)
- The code written serves as a useful reference for heuristic detection patterns
