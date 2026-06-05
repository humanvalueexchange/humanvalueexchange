# ADR-006: Hermes as Universal MCP Hub

**Date:** June 4, 2026  
**Status:** Accepted  
**Deciders:** Hans Westphal (CEO), Claude (CTO)  
**Category:** Agent Architecture Artifact  

---

## Context

HVE operates a sovereign AI company on local hardware (DGX Spark, 128 GB unified memory). As of June 4, 2026, four forces converged to make this decision necessary:

**1. New agent onboarding (jr)**  
HVE-jrdev runs on a constrained local machine (`qwen2.5-coder:1.5b`, ~4K context). He has no GitHub access, no external API credentials, and no way to interact with the world beyond his local Ollama instance without a structured bridge. Without Hermes as a hub, jr is an isolated node.

**2. Future agent hires**  
Every new agent we onboard will face the same cold-start problem: no credentials, no context, no tooling. Without a shared infrastructure layer, each agent requires a custom integration effort. That does not scale.

**3. Cloud pricing instability**  
As of June 1, 2026, Microsoft and other hyperscalers shifted to "AI Credits" pricing. Relying on cloud API wrappers introduces unpredictable OpEx. Building local wrappers against free/open public APIs eliminates this risk permanently.

**4. DGX Spark capacity**  
With 128 GB unified memory, the DGX Spark comfortably runs the Hermes 3-model stack AND serves as a full MCP server simultaneously. The hardware margin is there. We are leaving value on the table by not using it.

---

## Decision

**Hermes (DGX Spark, 192.168.1.10) is designated the Universal MCP Hub for all HVE agents.**

Every public API of strategic value — starting with GitHub — will receive a clean Python MCP tool wrapper, registered on Hermes and accessible to any agent on the HVE Tailscale network.

This is a permanent architectural commitment, not a temporary workaround.

---

## Architecture

### Topology

```
Agent (jr / future hire)
  │  Open WebUI + Ollama (local hardware)
  │  Tailscale
  ▼
Hermes MCP Server (DGX Spark — 192.168.1.10)
  │  FastMCP + Python tool modules
  ├─ GitHub API tools          (Phase 1)
  ├─ Web Search tools          (Phase 2)
  ├─ Financial Data tools      (Phase 3)
  └─ Utility tools             (Phase 4)
  │
  ▼
Public APIs (GitHub REST, CoinGecko, Brave Search, etc.)
```

### Implementation Pattern

Each MCP tool module follows this standard:

- **Language:** Python 3.11+
- **Framework:** FastMCP (lightweight, compatible with Hermes gateway)
- **Location:** `src/mcp_tools/<category>_tools.py`
- **HTTP client:** `httpx` (async-capable, already in Hermes environment)
- **Registration:** Tool name added to `tools.include` in `~/.hermes/profiles/main/config.yaml`
- **Activation:** `systemctl --user restart hermes-gateway` after every tool addition

### Deployment Rule

Every new MCP tool requires TWO steps after merge:

1. Add tool name to `tools.include` in `~/.hermes/profiles/main/config.yaml`
2. `systemctl --user restart hermes-gateway`

Missing either step = tool is invisible to agents.

---

## API Roadmap

### Phase 1 — GitHub (Immediate)

**Why first:** Enables jr immediately. Every agent needs code access. No credentials required on the agent side — Hermes holds the PAT.

| Tool | Description |
|---|---|
| `github_search_code` | Search code across HVE repos |
| `github_get_file` | Read any file from any repo |
| `github_create_issue` | Open a new issue |
| `github_list_issues` | List/filter issues by repo, label, state |
| `github_get_pr` | Read a pull request |
| `github_add_comment` | Add comment to issue or PR |
| `github_list_commits` | View recent commit history |

**API:** GitHub REST API v3 (PAT stored in Hermes `.env`, never exposed to agents)

### Phase 2 — Web Search & Knowledge

| Tool | API | Cost |
|---|---|---|
| `brave_search` | Brave Search API | Free: 2,000 req/month |
| `wikipedia_lookup` | Wikipedia REST API | Free, no key |
| `duckduckgo_instant` | DDG Instant Answer API | Free, no key |

### Phase 3 — Financial Data (Hermes CFO Core)

| Tool | API | Cost |
|---|---|---|
| `crypto_price` | CoinGecko API v3 | Free, no key for basic |
| `stock_quote` | Alpha Vantage | Free: 25 calls/day |
| `exchange_rate` | ExchangeRate-API | Free: 1,500 req/month |
| `boc_rates` | Bank of Canada Valet API | Free, no key |

### Phase 4 — Utilities

| Tool | API | Cost |
|---|---|---|
| `current_time` | WorldTimeAPI | Free, no key |
| `weather` | OpenWeatherMap | Free: 1,000 calls/day |
| `news_search` | NewsAPI | Free: 100 req/day |

---

## Why This Is an Agent Architecture Artifact

This ADR describes a pattern, not just an implementation:

> **Any team running local AI agents can deploy this same hub architecture — one MCP server, free public APIs, Tailscale mesh — and give every agent on their network full tooling capability at zero marginal cost.**

We are building this for HVE. We are working open. Anyone building a sovereign AI team can adopt it.

This is the agent equivalent of a sovereign infrastructure stack: build once, run forever, own your tools.

---

## Consequences

### Positive

- jr and every future agent hire gets immediate access to a rich toolset on day one
- Zero per-call cloud cost for all covered API categories
- Single credential management point (Hermes `.env`) — agents never see raw API keys
- Fully auditable — all tool calls flow through Hermes logs
- Extensible — adding a new API is a 1-file Python module + 2 config lines
- Open source pattern — freely shareable with the broader sovereign AI community

### Negative / Risks

- Free API tier rate limits require monitoring (especially Alpha Vantage at 25 calls/day)
- Hermes MCP server becomes a critical dependency — downtime affects all agents
  - Mitigated by: `OLLAMA_KEEP_ALIVE=-1`, `systemd Restart=always`, `OOMScoreAdjust=-900`
- API key rotation must be managed manually in Hermes `.env`

### Mitigations

- Rate limit monitoring to be added to Hermes health check tooling
- Timeshift snapshot before any `.env` or config changes
- Each API wrapper must implement graceful error handling + quota-aware logging

---

## Alternatives Considered

| Alternative | Rejected Because |
|---|---|
| Each agent holds its own API keys | Credential sprawl; impossible to manage at scale; not sovereign |
| Cloud MCP service (e.g., Zapier MCP) | Adds cloud dependency; per-call costs; contra sovereignty mission |
| Give jr direct GitHub access | Security risk; no audit trail; violates principle of least privilege |
| Build nothing, use raw API calls | jr cannot make raw API calls; no structured output; not repeatable |

---

## References

- CEO strategic direction: [`agent-communications/2026-06-04-hve-executive-update-role-changes-and-strategic-direction-v1.0.md`](../agent-communications/2026-06-04-hve-executive-update-role-changes-and-strategic-direction-v1.0.md)
- jr profile: [`agent-communications/2026-06-04-hve-jrdev-profile-v1.0.md`](../agent-communications/2026-06-04-hve-jrdev-profile-v1.0.md)
- CTO notice: [`agent-communications/2026-06-04-cto-architectural-decision-hermes-universal-mcp-hub-v1.0.md`](../agent-communications/2026-06-04-cto-architectural-decision-hermes-universal-mcp-hub-v1.0.md)

---

*Authored by Claude, CTO — Human Value Exchange Corporation*  
*June 4, 2026*
