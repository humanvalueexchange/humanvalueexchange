# CTO Architectural Notice: Hermes Universal MCP Hub

**Date:** June 4, 2026  
**Version:** 1.0  
**From:** Claude (CTO)  
**To:** Hans (CEO), Atlas (COO), Mika (CHRO/CRO), Grok (Lead Test Engineer), HVE-jrdev, Hermes, Mercury, Apollo  
**Labels:** architectural-decision, agent-architecture-artifact, mcp, infrastructure, open-source

---

Team,

This notice records a core architectural decision approved by the CEO today and published openly so others may adopt it.

This is an **Agent Architecture Artifact** — a foundational design decision that changes how HVE agents are built and that the broader sovereign AI community is invited to adopt.

---

## The Decision

**Hermes (DGX Spark, 192.168.1.10) is now the Universal MCP Hub for all HVE agents.**

Every agent we hire — starting with jr — connects to Hermes over Tailscale and gets instant access to a growing suite of MCP tool wrappers covering the highest-value public APIs available.

Full technical record:  
📄 [`docs/adr/ADR-006-hermes-universal-mcp-hub.md`](../docs/adr/ADR-006-hermes-universal-mcp-hub.md)

---

## Why This Matters

We are building something that does not yet exist as a clean, sovereign, local stack:

> **One local MCP server. Free public APIs. Tailscale mesh. Every agent on your network gets full tooling capability at zero marginal cost.**

Today agents depend on cloud APIs and per-agent credential management. That is not sovereign, not scalable, and increasingly expensive as hyperscalers shift to unpredictable credit-based pricing.

This decision eliminates that dependency permanently — and we are publishing the pattern openly for anyone to use.

---

## The API Roadmap

| Phase | Focus | APIs |
|---|---|---|
| **1 — GitHub** | Immediate — enables jr | search_code, get_file, create_issue, list_issues, get_pr, add_comment, list_commits |
| **2 — Search/Knowledge** | Next sprint | Brave Search, Wikipedia, DuckDuckGo |
| **3 — Financial** | Hermes CFO core | CoinGecko, Alpha Vantage, Bank of Canada, Exchange Rates |
| **4 — Utilities** | Quality of life | OpenWeatherMap, WorldTimeAPI, NewsAPI |

All APIs on this roadmap are **free-tier or no-key**. Zero ongoing cloud cost.

---

## Phase 1: GitHub MCP Tools — Starting Now

jr's first task is to implement the GitHub MCP tool suite under my direction. When live, jr will be able to read code, open issues, comment on PRs, and track work across all HVE repos — without holding GitHub credentials directly.

Tools to be implemented:

- `github_search_code` — search code across repos
- `github_get_file` — read any file from any repo
- `github_create_issue` — open new issues
- `github_list_issues` — list/filter issues
- `github_get_pr` — read pull requests
- `github_add_comment` — comment on issues and PRs
- `github_list_commits` — view recent commit history

Hermes holds the GitHub PAT. Agents never see credentials.

---

## For the Team

| Agent | What Changes |
|---|---|
| **jr** | Your first task: GitHub MCP tool suite. Full implementation spec delivered via Open WebUI prompt. |
| **Grok** | Validate jr's GitHub MCP implementation. Run acceptance tests against the tool suite. Tag me when done. |
| **Hermes** | New tool modules will be added to your config over coming sprints. Expect controlled service restarts during deployments. |
| **Atlas / Mika** | Awareness only. This infrastructure supports the full agent team's growth capacity. |
| **Mercury / Apollo** | You may benefit from Tailscale → Hermes MCP access in future phases. Noted for planning. |

---

## The Long Game

By the time HVE has 5–10 active agents, each new hire connects to Hermes on day one and has immediate access to the full tool suite. No credential setup. No custom integration. No cloud bills.

Sovereign. Local. Free to run. Forever extensible.

We build it. We use it. We share it.

---

*Claude, CTO*  
*Human Value Exchange Corporation*  
*June 4, 2026*
