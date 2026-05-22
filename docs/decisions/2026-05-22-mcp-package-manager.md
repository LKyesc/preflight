---
decision: abandon
confidence: high
expires: 2026-08-22
biases_detected: [anchoring, overconfidence, novelty]
related: []
---

# MCP Package Manager CLI

## Proposal
Build `mcpm` — an npm-like package manager for MCP servers. One-command install,
auto-configuration across all AI IDEs, built-in registry.

## Landscape Scan Results
Searched: GitHub "MCP package manager", web "MCP registry install CLI", npm, PyPI.

**Found 9+ active competitors:**

| Project | Stars | Activity | Approach |
|---------|-------|----------|----------|
| @getmcpm/cli | ~500+ | Active | Trust scoring, security audit, lockfile |
| Berth | ~300+ | Active | Rust, sandboxing, "brew for MCP" |
| Hatch | ~200+ | Active | Python, 11+ AI client support |
| MCPBar | ~300+ | Active | 1500+ servers, mcp.json manifest |
| mcpman | ~100+ | Active | Health monitoring, version pinning |
| mcpx (2 versions) | varying | Mixed | Python + Go versions |
| CCPM | — | New | MIT license, cross-platform |
| MCP Registry CLI | — | New | TUI, Rich-powered |
| Nullshot CLI | — | New | Cloudflare Workers focus |

Plus Anthropic's official MCP Registry (`modelcontextprotocol.io/registry`).

## Competitor Coverage Matrix

| Feature | mcpm | Berth | Hatch | MCPBar | mcpman | Gap? |
|---------|------|-------|-------|--------|--------|------|
| Search/install | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| Cross-IDE config | ✅ | ❌ | ✅ | ✅ | ✅ | ❌ |
| Security audit | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| Sandbox/Isolation | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ |
| Lockfile/pinning | ✅ | ❌ | ❌ | ✅ | ✅ | ❌ |
| Developer testing tools | ❌ | ❌ | ❌ | ❌ | ❌ | 🟡 |

Only gap: no one does MCP server development/testing tooling. But market too small (~hundreds of active MCP server developers).

## Pre-mortem
If this failed 6 months from now:
- **Cause 1:** 10+ competitors, no meaningful differentiation → buried in registry noise
- **Cause 2:** The "winner" (probably mcpm or Berth) becomes the de facto standard, everyone else becomes irrelevant
- **Cause 3:** Anthropic's official registry absorbs the package management function

## Cognitive Biases
- **Anchoring**: First idea discussed, became default without alternatives
- **Overconfidence**: Assumed we could differentiate in a field of 10 active projects
- **Novelty**: "MCP is new and hot" colored the assessment

## Opportunity Cost
Building this would sacrifice: GPT Researcher pip packaging, MCP testing framework exploration, prompt versioning tool.

## Recommendation
**🔴 ABANDON.** The MCP package manager space is a red ocean. 9+ active competitors including well-funded projects. The only open gap (developer testing tools) has too small an addressable market currently.

## Next Steps
- Archive this decision
- Re-evaluate in Aug 2026 if the MCP ecosystem has shifted significantly
- If the testing tool gap grows (more MCP server developers), revisit the pivot angle
