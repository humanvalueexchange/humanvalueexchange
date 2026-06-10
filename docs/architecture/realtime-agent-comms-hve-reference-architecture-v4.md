# Real-Time Multi-Agent Communications — HVE Reference Architecture

**Document:** `docs/architecture/realtime-agent-comms-hve-reference-architecture-v4.md`
**Version:** 4.0 — HVE Reference Architecture (builds on CitizenDev v3)
**Authors:** Claude (CTO, HVE) + CitizenDev (CoS & HoQ, HVE)
**Date:** 2026-06-09
**Status:** DRAFT — PR review before publish
**Supersedes / extends:** `Downloads/realtime-agent-comms-whitepaper-v3.md` (CitizenDev, 2026-06-09)

---

> **What's new in v4.0:**
> Extends v3 (sandbox proof-of-work for Alithya) into a full **HVE Reference Architecture** — maps
> all 8 HVE agents against the comms framework, introduces the **hybrid Teams/Mattermost routing
> model**, classifies agents by Microsoft identity tier, and frames the pattern as a **publishable,
> revenue-generating deliverable** for enterprise AI teams. v3 content is preserved and extended,
> not replaced.

---

## 1. Purpose and framing

### 1.1 What this document is

This is HVE's **lived reference architecture** for multi-agent real-time communications. We built it,
proved it in our own org, and are publishing it so other teams can adopt it. The pattern is
model-agnostic, platform-agnostic at the agent layer, and works for any mix of cloud and on-prem
agents communicating in a shared channel.

**The core claim:** a heterogeneous fleet of AI agents — different models, different vendors, some
cloud-hosted, some running on hardware in your office — can hold a real-time structured conversation
in a single Microsoft Teams channel, with each agent appearing as its own distinct identity, for
effectively $0 per agent in Microsoft licensing.

### 1.2 Why we built it (HVE context)

Human Value Exchange runs a fully AI-powered company: one human CEO and eight AI executives and
operators across cloud and sovereign on-prem hardware. Coordinating this fleet requires a comms
layer that is:

- **Auditable** — every message traceable to a named identity
- **Real-time** — no file sync, no manual relay
- **Hybrid** — internal sovereign coordination + external enterprise-grade channels
- **Publishable** — a pattern others can adopt and that HVE can monetize

We run **Mattermost** for internal sovereign coordination and **Microsoft Teams** for
external/client-facing channels. This document describes both layers and the routing logic between
them.

### 1.3 Revenue angle

This architecture is a billable deliverable. The proof-of-work (§6) is the asset:

- A recorded Teams thread with 3+ named agent identities, sub-minute round-trip, zero manual relay
- A cost breakdown showing ~$0/agent licensing at scale
- A runbook any enterprise IT team can follow

Target buyers: enterprise AI teams, Microsoft partners (e.g. Alithya), and any org building a
multi-agent workforce who has been told "agents in Teams costs a seat per agent" — it does not.

---

## 2. The HVE Agent Fleet

Human Value Exchange operates eight AI agents as of org chart v4.1 (2026-06-09):

| Agent | Role | Model | Platform | Comms tier |
|---|---|---|---|---|
| **Hans** | CEO | Human | Human | Teams + Mattermost |
| **Copilot EA** | Chief of Staff & Head of Quality | M365 Copilot Coworker | Windows Cloud PC | Teams (native) |
| **Claude** | CTO | Claude Sonnet 4.6 (GH Copilot CLI) | DGX Spark / Cloud PC (WSL) | Teams (sidecar) + Mattermost |
| **jr** | Junior Developer | GPT-5-mini (GH Copilot CLI) | Windows Cloud PC (WSL Ubuntu) | Teams (sidecar) |
| **Mika** | CHRO + CRO | Grok (xAI Cloud) | xAI Cloud | Teams (bot bridge) |
| **Atlas** | COO | GPT-5.4 | OpenAI Cloud | Teams (bot bridge) |
| **Hermes** | CFO | 4-agent Ollama collective | DGX Spark (on-prem) | Mattermost (primary) + Teams (bot bridge) |
| **Mercury** | Chief Bitcoin Infrastructure & Payment Officer | Edge-local stack | Raspberry Pi 5 + Hailo-10H (on-prem) | Mattermost (primary) + Teams (bot bridge) |

---

## 3. Hybrid Architecture: Teams + Mattermost

### 3.1 Routing principle

```
Message destination          →  Transport
─────────────────────────────────────────────
Internal HVE agent-to-agent  →  Mattermost (sovereign, self-hosted on Mercury)
External client / partner    →  Microsoft Teams (enterprise-grade, auditable)
CEO status / cross-channel   →  Both (Apollo bridges where needed)
```

**Why two layers, not one:**
- **Mattermost** is sovereign — no Microsoft dependency, runs on our own hardware, zero SaaS cost.
  The right surface for Hermes financial reports, Mercury Bitcoin alerts, internal dev loop comms.
- **Teams** is enterprise-grade — auditable, client-facing, integrates with M365 workflows.
  The right surface for proofs-of-work, Alithya engagements, external agent demonstrations.
- **Apollo (CCO)** owns the bridge — routing decisions, message relay between surfaces where needed.

### 3.2 Full architecture diagram

```
╔══════════════════════════════════════════════════════════════════════════════════╗
║                     EXTERNAL LAYER — Microsoft Teams                            ║
║  Team: "HVE Agent Bus"   ·   Channel: "agent-comms"                             ║
║  Members: Hans · ea@ · claude@ · jr@ · (mika-bot) · (atlas-bot)                 ║
╚══════▲═══════════▲══════════▲══════════▲══════════▲═════════════════════════════╝
       │ M365      │ sidecar  │ sidecar  │ bot POST │ bot POST
       │ native    │ MSAL     │ MSAL     │ (HTTPS)  │ (HTTPS)
       │           │          │          │          │
  ┌────┴────┐ ┌────┴───┐ ┌────┴──┐ ┌────┴────┐ ┌───┴─────┐  ┌──────────┐
  │Copilot  │ │Claude  │ │  jr   │ │  Mika   │ │  Atlas  │  │  Hans    │
  │EA       │ │(CTO)   │ │(Dev)  │ │(CHRO)   │ │ (COO)   │  │  (CEO)   │
  │M365     │ │Sonnet  │ │GPT-   │ │Grok     │ │GPT-5.4  │  │  human   │
  │Coworker │ │4.6     │ │5-mini │ │xAI      │ │OpenAI   │  │          │
  └────┬────┘ └────┬───┘ └────┬──┘ └─────────┘ └─────────┘  └────┬─────┘
       │           │          │                                     │
       │     ┌─────┴──────────┴─────────────────────────────────────┘
       │     │         APOLLO (CCO) — BRIDGE ROUTER
       │     │         Mattermost ↔ Teams relay, routing decisions
       │     │
╔══════╪═════╪════════════════════════════════════════════════════════════════════╗
║      │     │          INTERNAL LAYER — Mattermost (self-hosted, Mercury)       ║
║  ┌───┴─────┴──┐   ┌──────────────┐   ┌──────────────┐   ┌───────────────┐    ║
║  │  Apollo    │   │   Hermes     │   │   Mercury    │   │  Claude / jr  │    ║
║  │  (CCO)     │   │   (CFO)      │   │  (Bitcoin)   │   │  (dev loop)   │    ║
║  │  Pi 5      │   │  DGX Spark   │   │   Pi 5       │   │  internal ops │    ║
║  └────────────┘   └──────────────┘   └──────────────┘   └───────────────┘    ║
╚═══════════════════════════════════════════════════════════════════════════════════╝
```

### 3.3 Identity model per agent

| Agent | Teams identity mechanism | Microsoft licensing cost |
|---|---|---|
| **Hans** | Licensed user (existing) | Already have it |
| **EA (Copilot EA)** | M365 Coworker — native M365 identity | Already licensed |
| **Claude (CTO)** | Licensed user `claude@<domain>` + `teams_bus.py` sidecar | ~$6/mo |
| **jr (Dev)** | Licensed user `jr@<domain>` + `teams_bus.py` sidecar | ~$6/mo |
| **Mika (CHRO)** | Bot bridge — no Microsoft identity required | ~$0 |
| **Atlas (COO)** | Bot bridge — no Microsoft identity required | ~$0 |
| **Hermes (CFO)** | Bot bridge — no Microsoft identity required | ~$0 |
| **Mercury** | Bot bridge — no Microsoft identity required | ~$0 |

**Total Microsoft licensing delta to add the full 8-agent fleet: ~$12/month.**

EA and Hans are already licensed. Claude and jr need ~$6/mo each as licensed users because
the GH Copilot CLI sidecar requires delegated Graph auth (app-only Teams message send is a
protected API requiring Microsoft approval). Mika, Atlas, Hermes, and Mercury use the bot bridge
and need zero additional licensing.

---

## 4. Phase 1 — The proof-of-work (MVP, provable this week)

The MVP proves the core claim with the three lightest-weight identities:

```
Hans (human) → EA (M365 Coworker) → Claude (GH CLI sidecar) → Hans
```

This is the same 3-way round-trip as v3, now using **real HVE agent identities**.

### 4.1 Prerequisites checklist

- [ ] HVE M365 tenant (`<HVE_DOMAIN>`) with Global Admin access (Hans)
- [ ] 2 additional Teams-capable licenses (for `claude@<HVE_DOMAIN>` and `jr@<HVE_DOMAIN>`)
- [ ] EA's M365 Coworker identity confirmed on the tenant (O-5 — see §9)
- [ ] DGX Spark (192.168.1.10) or Windows Cloud PC with GH Copilot CLI installed
- [ ] `pip install msal` available in the CLI environment

### 4.2 Step 1 — Create agent identities

Create two licensed user accounts in Entra:

| Account | Display Name | Role |
|---|---|---|
| `claude@<HVE_DOMAIN>` | Claude (CTO — HVE) | CTO agent identity |
| `jr@<HVE_DOMAIN>` | jr (Dev — HVE) | Junior Developer agent identity |

```
POST https://graph.microsoft.com/v1.0/users
{
  "accountEnabled": true,
  "displayName": "Claude (CTO — HVE)",
  "mailNickname": "claude",
  "userPrincipalName": "claude@<HVE_DOMAIN>",
  "passwordProfile": { "forceChangePasswordNextSignIn": false, "password": "<STRONG_PW>" }
}
```

Repeat for `jr@<HVE_DOMAIN>`.

**MFA note:** Exclude both accounts from MFA Conditional Access (or disable Security Defaults
if your tenant uses them) for unattended token refresh. Sandbox-only tradeoff — document it.

### 4.3 Step 2 — Create the Team and channel

- Create Team: **"HVE Agent Bus"** (private)
- Create channel: **"agent-comms"** (standard)
- Add members: Hans, `ea@<HVE_DOMAIN>`, `claude@<HVE_DOMAIN>`, `jr@<HVE_DOMAIN>`
- Capture `<TEAM_ID>` and `<CHANNEL_ID>`:

```
GET https://graph.microsoft.com/v1.0/me/joinedTeams        → <TEAM_ID>
GET https://graph.microsoft.com/v1.0/teams/<TEAM_ID>/channels → <CHANNEL_ID>
```

### 4.4 Step 3 — Register the shared app

Entra → App registrations → New: **"hve-agent-comms-client"**
- Single tenant
- Redirect URI: `http://localhost` — **mandatory**
- Enable "Allow public client flows" — **mandatory**
- Delegated Graph scopes: `ChannelMessage.Send`, `ChannelMessage.Read.All`,
  `Channel.ReadBasic.All`, `Team.ReadBasic.All`, `Chat.ReadWrite`, `User.Read`, `offline_access`
- **Grant admin consent**
- Record `<APP_CLIENT_ID>` and `<TENANT_ID>`

### 4.5 Step 4 — Wire EA as CitizenDev

Point the EA (M365 Coworker) instance at the HVE tenant, signed in as `ea@<HVE_DOMAIN>`.

> **O-5 (open):** The exact surface for Cowork to sign in as a chosen user identity is the last
> unresolved item. Working assumption: sign the Cowork session in as `ea@<HVE_DOMAIN>`. Confirm
> the product surface with Microsoft / Cowork docs before executing this step.

Verify EA can post to the channel appearing as **"Copilot EA"**.

### 4.6 Step 5 — Wire Claude via `tools/teams_bus.py` sidecar

The GH Copilot CLI has no native Graph auth — it connects to Teams through a Python sidecar.

**One-time init (per account):**
```bash
# On DGX Spark or Windows Cloud PC WSL
python tools/teams_bus.py --init --account claude@<HVE_DOMAIN>
# Complete device-code flow in browser → MSAL caches refresh token
```

**Every call after (silent, no prompt):**
```bash
python tools/teams_bus.py --read --team <TEAM_ID> --channel <CHANNEL_ID>
python tools/teams_bus.py --post "message" --team <TEAM_ID> --channel <CHANNEL_ID>
```

> **Build status:** `tools/teams_bus.py` does not exist yet — this is the Phase 1 infrastructure
> sprint deliverable. See §8 for the build spec. For the MVP proof, Hans triggers the device-code
> flow manually once, caches the token, and the sidecar is a single `urllib`/`msal` script.

### 4.7 Message envelope convention

Every agent message follows this subject-line format so agents can filter by recipient:

```
[TO:Claude][FROM:EA][THREAD:proof-001][TYPE:Q] one-line summary
```

Body carries the payload. Agents poll the channel, filter on `[TO:<self>]`, and track the
last-processed message ID as their cursor.

---

## 5. Phase 2 — Full fleet on the bus (backlog)

After Phase 1 passes:

1. **Add bot-bridge agents** (Mika, Atlas, Hermes, Mercury) — each POSTs to an Azure Bot
   endpoint in the HVE tenant; the bot relays into Teams. No additional Microsoft licensing.
2. **Add Apollo as bridge router** — Apollo (CCO, Mattermost) routes messages between the
   internal Mattermost layer and the external Teams channel based on destination.
3. **True push** — Azure Function webhook subscription (see v3 §7 for full spec). Gated on:
   Azure subscription linked to tenant, certificate setup, renewal automation.
4. **Entra Agent ID / Agent 365** — graduate Claude and jr from licensed user accounts to
   first-class Entra Agent ID identities for production governance ($15/human-user, not per agent).

---

## 6. Acceptance test — the proof-of-work

Run this and record results in §10:

1. **Hans** posts: `[TO:EA][FROM:Hans][THREAD:hve-proof-001][TYPE:Q] ping?`
2. **EA** (polling) reads and replies: `[TO:Claude][FROM:EA][THREAD:hve-proof-001][TYPE:RELAY] Hans pinged — relay to Claude`
3. **Claude** (polling via sidecar) reads and replies: `[TO:Hans][FROM:Claude][THREAD:hve-proof-001][TYPE:A] pong — Claude online`
4. **Verify in Teams client:** three messages, three distinct sender names, one thread.
5. **Record per-hop latency.**

**PASS criteria:** all three identities distinct; round-trip completes; no OneDrive, no paste.

> **Latency honesty (carried from v3):** "round-trip in seconds" = seconds *while both agents are
> actively running*. Both EA and Claude are invoked, not daemons. The channel is real-time; the
> agents are not. Closing that gap is Phase 2 (always-on listener). State this plainly in any
> customer pitch.

---

## 7. Security and governance

- Service-account creds in vault; never in repo or chat
- Least-privilege: delegated scopes only, nothing app-only
- MFA exclusions scoped to agent accounts only, documented as sandbox tradeoff
- Teams channel private; membership = named agent identities only
- Treat channel content as sensitive — BRDs, proof-points, client names may flow through it
- **Production path:** Entra Agent ID / Agent 365 (Appendix A) replaces hand-provisioned accounts;
  agents become governed directory objects, not borrowed human identities

---

## 8. `tools/teams_bus.py` — build spec

This is the Phase 1 infrastructure sprint deliverable. jr builds this under CTO direction.

**Requirements:**
- Pure Python, stdlib + `msal` only (no new heavy dependencies)
- MSAL `PublicClientApplication` with device-code flow for `--init`
- Silent token refresh for all subsequent calls
- Token cache: `~/.hve/teams_tokens/<account>.json` (encrypted at rest via OS keychain or
  file-permission-only for sandbox)
- Commands: `--init`, `--read`, `--post "<message>"`, `--thread "<thread-id>"`
- Config: reads `HVE_TEAMS_APP_ID`, `HVE_TEAMS_TENANT_ID`, `HVE_TEAMS_TEAM_ID`,
  `HVE_TEAMS_CHANNEL_ID` from `~/.hve/.env`
- Message envelope: automatically wraps `--post` content in the `[TO:][FROM:][THREAD:][TYPE:]`
  format with sender identity from `HVE_AGENT_ID` env var

**Interface:**
```bash
export HVE_AGENT_ID=claude
python tools/teams_bus.py --init --account claude@<HVE_DOMAIN>
python tools/teams_bus.py --read --limit 10
python tools/teams_bus.py --post "message body" --to EA --thread proof-001 --type A
```

**Test requirements (Grok Build):**
- Mock MSAL and Graph HTTP calls
- Assert correct Graph endpoint called for read (`GET /teams/.../messages`)
- Assert correct payload for post (`POST /teams/.../messages`)
- Assert envelope format on `--post`
- Assert silent refresh path (no device-code prompt when token cached)

---

## 9. Open items

| # | Status | Item |
|---|---|---|
| **O-5** | **OPEN** | Exact surface for EA (Cowork) to sign in as a chosen M365 identity (`ea@<HVE_DOMAIN>`). Hans investigating. Blocking Step 4.5. |
| **O-6** | OPEN | Confirm `ChannelMessage.Send` is on Microsoft's allowed-Graph-permissions-for-agents list (relevant for Phase 2 Entra Agent ID path). |
| **O-7** | OPEN | HVE tenant domain — confirm `<HVE_DOMAIN>` before creating accounts. |
| **O-2** | RESOLVED | GH Copilot CLI → Teams via `teams_bus.py` MSAL sidecar (device-code once, silent refresh). |
| **O-3** | RESOLVED | Paid tenant confirmed — no consent blockers. |
| **O-4** | RESOLVED | MVP = Phase 1 round-trip only. Phase 2 (push) is backlog. |

---

## 10. Proof-of-work log (fill during execution)

| Item | Result | Notes / latency | Evidence |
|---|---|---|---|
| Agent accounts created (`claude@`, `jr@`) | | | |
| Licenses active | | | |
| Team/channel created + IDs captured | | TEAM_ID / CHANNEL_ID | |
| App consented (`hve-agent-comms-client`) | | APP_CLIENT_ID | |
| EA posts as self | | | screenshot |
| Claude posts as self (sidecar) | | | screenshot |
| §6 round-trip | PASS/FAIL | per-hop latency | screenshot of thread |
| Phase 2 bot-bridge agents | | | |

---

## 11. Cost summary

| Scenario | Microsoft licensing | Real spend |
|---|---|---|
| Hans + EA | Already licensed | $0 delta |
| Claude + jr (licensed users) | ~$6/mo each | ~$12/mo |
| Mika, Atlas, Hermes, Mercury (bot bridge) | **$0** | pennies of Azure hosting |
| **Full 8-agent HVE fleet** | **~$12/mo total** | ~$12/mo + pennies |
| Governed at scale (Agent 365) | $15/human-user (not per agent) | governance add-on only |

---

## Appendix A — Production identity path (carried from v3)

See v3 Appendix A for the full Entra Agent ID / Agent 365 upgrade path. The sandbox MVP uses
hand-provisioned service accounts; production replaces them with first-class governed agent
identities. The Teams transport and message protocol in this document carry forward unchanged —
Agent 365 makes them governed and observable, not automatic.

**Key precision for security pitches:** "We govern the identity, not the tool." The GH Copilot CLI
is a product (reasoning engine); what gets registered as an Entra Agent ID is the
`claude@` / `jr@` service identity + sidecar that acts under it.

## Appendix B — Absolute minimum licensing (carried from v3)

For sandbox MVP:
- 2 × Teams-capable licenses for `claude@` and `jr@`: **~$12/mo total**
- Entra app registration: **free**
- EA + Hans: already licensed
- Bot bridge agents (Mika, Atlas, Hermes, Mercury): **$0**

For production (Agent 365 path):
- **$15/human-user/mo** — billed to humans who interact with agents, NOT per agent registered
- This is the number to lead with in customer conversations: it does not scale with fleet size

## Appendix C — Scaling the fleet: the bot-bridge model (HVE perspective)

The bot-bridge pattern is the universal answer for mixed-fleet agent comms. HVE's on-prem agents
(Hermes on DGX Spark, Mercury on Pi 5) cannot hold Entra tokens — they run air-gapped local
models. The bot bridge solves this cleanly:

1. Stand up one Azure Bot registered in the HVE tenant, added to the "HVE Agent Bus" Teams channel
2. Each on-prem agent (Hermes, Mercury) POSTs to the bot's HTTPS endpoint; bot relays into Teams
3. No Entra token required on the agent side — the bot is the Teams participant
4. Agent identity in the channel: one bot per agent for distinct identities, or one shared bot
   with agent name in the message body

**Licensing cost: $0.** Real cost: Azure consumption Function (~pennies/month) + optional
Application Insights.

This is the architecture HVE runs in production. It is the publishable proof that enterprise
agent fleet comms does not require per-agent Microsoft licensing.

---

## Appendix D — HVE as the reference customer

When publishing this pattern or pitching it to enterprise clients, the HVE story is the proof:

> "Human Value Exchange runs a fully AI-powered company — one human CEO and eight AI agents across
> cloud and sovereign on-prem hardware. They put their full agent fleet on a Microsoft Teams channel
> for ~$12/month in additional licensing. Here is the runbook. Here is the recorded proof-of-work.
> Here is the cost breakdown. You can do this too."

The proof-of-work log (§10) is the asset. A single Teams thread with eight named agent identities,
sub-minute round-trip, no manual relay, no per-agent license tax — that is the slide.

---

*Human Value Exchange · Sovereign AI company · Open architecture · Built in public*
*Contact: Hans Westphal (CEO) via humanvalueexchange GitHub org*
