# ADR-007: ProDev/CitizenDev Skills Distribution via WSL

**Date:** June 6, 2026  
**Status:** Accepted  
**Deciders:** Hans Westphal (CEO), Claude (CTO)  
**Category:** Agent Architecture Artifact

---

## Context

On June 6, 2026, Hans and I (Claude, CTO) ran a full-day working session deploying the HVE Tools→Knowledge→Skills architecture (ADR-005) into a commercial sales context. During that session, a structural insight emerged that extends beyond the immediate engagement and belongs in the HVE pattern library permanently.

The insight is this: **the 40-year wall between ProDev and CitizenDev environments — Unix/Linux on one side, Windows on the other — has already been erased. The industry just hasn't fully acted on it yet.**

The mechanism: **WSL (Windows Subsystem for Linux).**

This ADR captures that insight as a durable architectural pattern and formalizes how HVE will use it — both internally and as an open contribution to the sovereign AI community.

---

## The 40-Year Problem

Since the early 1980s, the professional developer (ProDev) and the knowledge worker (CitizenDev) have operated in fundamentally different computational environments:

```
1985 → 2024:

ProDev                              CitizenDev
────────────────────────────────    ────────────────────────────────
Unix / Linux terminal               Windows GUI
Python, bash, git, curl             Office, Teams, browsers
Custom tools built from source      Apps installed by IT
SKILL.md files, SOUL.md, AGENTS.md  No equivalent
Sovereign infrastructure            Managed devices, cloud tenants
"I write tools"                     "I use tools"
        ↕
    THE WALL
(bridges were always expensive:
VMs, containers, SSH jump hosts,
admin tickets, dual-boot machines)
```

This wall has real costs:
- Tools built by ProDevs cannot be distributed to CitizenDevs without IT intermediation
- CitizenDevs cannot adopt sovereign agent tooling without becoming ProDevs first
- AI skills built for CLI environments (SKILL.md, SOUL.md, MCP servers) are invisible to Windows-native workers
- Adoption of ProDev-built capabilities stalls at the boundary

---

## The Accidental Unlock

Microsoft shipped WSL as a Windows compatibility layer for developers who needed Linux tooling. They did not intend it as an enterprise AI skills distribution infrastructure. They solved the 40-year problem by accident.

**WSL properties that matter:**

| Property | Significance |
|----------|-------------|
| Ships on every Windows 10/11 Pro machine | Zero new hardware required |
| No admin required to activate on modern Windows Pro | Zero IT ticket required |
| Native Ubuntu — not a VM | Same binaries, same filesystem, same performance |
| Full Windows interop via `/mnt/c/` | Windows and Linux share files natively |
| Runs GitHub Copilot CLI natively | The ProDev toolchain is available to anyone |
| `COPILOT_SKILLS_DIRS` env var | Skills in any directory become CLI slash commands |

**The implication:** Every CitizenDev at any organization running Windows Pro already has the ProDev environment available. The wall is gone. They just haven't walked through yet.

---

## Decision

**Adopt the ProDev/CitizenDev via WSL pattern as a first-class HVE architectural pattern.**

### The Distribution Model

```
ProDev (builds)                           CitizenDev (consumes)
───────────────────────────────────────   ──────────────────────────────────
Ubuntu WSL (or native Linux/macOS)         Ubuntu WSL on Windows Pro
Builds skills: /agent/skills/SKILL.md      Runs ONE install command
Builds knowledge: /knowledge/              Gets all skills as slash commands
Builds tools: /scripts/                   Updates via: git -C ~/cowork pull
Commits to GitHub repo             ──────►  
Pushes updates                             Same skills, same CLI, same results
```

### The One-Command Install

Modeled after Nous Research Hermes Agent's installer (MIT licensed):

```bash
curl -fsSL https://raw.githubusercontent.com/{org}/{repo}/main/install.sh | bash
```

This script:
1. Verifies WSL/Linux environment
2. Installs GitHub CLI (`gh`) if not present
3. Clones the ProDev skills repo to `~/cowork/` (or equivalent)
4. Sets `COPILOT_SKILLS_DIRS` and `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` in `~/.bashrc`
5. Walks through GitHub auth
6. Optionally: M365/API auth for live data connections
7. Prints skill activation summary

**After install, the CitizenDev experience:**

```bash
# Open Windows Terminal → Ubuntu (WSL)
gh copilot              # or: copilot

/rfp-response           # Skill fires immediately — grounded, knowledge-embedded
/discovery-playbook     # Another skill, another slash command
/morning-briefing       # Live D365 + Planner data, if M365 auth configured
```

No IT ticket. No admin. No VM provisioning. No Copilot Studio license. No admin consent workflow.

---

## The Knowledge Embedding Principle

This pattern is NOT simply "put prompts in a file." The power is in what is embedded in the skills.

A skill without knowledge is **reactive** — it gives generic advice.  
A skill with knowledge is **strategic** — it gives advice grounded in specific, proven, non-replicable context.

The ProDev role is to:
1. Ingest source material (real proposals, real outcomes, real data)
2. Extract patterns (win themes, language banks, proof points, voice signatures)
3. Embed those patterns in the skill — not referenced externally, but baked in
4. Commit to the repo

**CitizenDevs cannot build this by themselves.** They can activate skills. They cannot build them. That is the ProDev value — and it compounds with every new knowledge asset ingested.

This is the same architecture as ADR-005 (Tools→Knowledge→Skills), applied to the distribution problem specifically. ADR-005 defined the layers. ADR-007 defines how the layers reach CitizenDevs.

---

## Scale Invariance

This pattern holds at every scale:

| Scale | ProDev | CitizenDev | Distribution |
|-------|--------|------------|--------------|
| Solo | One person, both roles | Same person on same machine | `COPILOT_SKILLS_DIRS` env var |
| Small team | 1 ProDev, 3-5 CitizenDevs | Windows machines with WSL | `install.sh` + private GitHub repo |
| Enterprise | ProDev team, 100+ CitizenDevs | Managed Windows fleet with WSL enabled | `install.sh` + org GitHub repo + MDM |
| Partner network | Shared ProDev, 1000+ sellers | All Microsoft partner Windows machines | Public GitHub repo + `install.sh` |

The implementation changes at each scale. The architecture does not.

This is the same scale-invariance property documented in the June 1, 2026 GitHub Control Plane memo — the cheapest version and the most regulated version are the same architecture. HVE proves it small. It lifts large without re-architecting.

---

## The Boundary Separation Principle (Critical)

This pattern was validated during a commercial engagement. The implementation details of that engagement belong to the client organization. The **pattern** belongs to HVE.

Per the June 1, 2026 boundary rule:
- Client-specific skills, knowledge, and data: client's sovereign repo, client's identity
- The architectural pattern, the install script design, the SKILL.md conventions: HVE intellectual property, openly published

HVE is the proof-of-pattern at small scale. The commercial engagement is the proof-of-pattern in enterprise sales context. Both validate the architecture. Neither contaminates the other.

---

## The SOUL.md / AGENTS.md / MEMORY.md Convention

The Hermes Agent project (Nous Research, MIT licensed) established a file convention for agent identity and memory that HVE adopts:

| File | Purpose | Loaded by |
|------|---------|-----------|
| `SOUL.md` | Agent identity, operating principles, core persona | Session start |
| `MEMORY.md` | Persistent cross-session facts | Session start |
| `USER.md` | Model of the user (goals, style, context) | Session start |
| `AGENTS.md` | Workspace config for any AI agent | Copilot CLI auto-loads |
| `skills/*/SKILL.md` | Skill definitions (YAML frontmatter + markdown) | Via `COPILOT_SKILLS_DIRS` |

**Copilot CLI auto-loads `AGENTS.md` from the git root.** This means any repo with an `AGENTS.md` file gives the agent full workspace context automatically — no manual setup, no session-start instructions.

This convention is now part of the HVE standard for ProDev environments.

---

## Consequences

**Positive:**
- Zero marginal distribution cost — ProDev ships skills, CitizenDevs get them via `git pull`
- No IT dependency for skill activation on Windows Pro with WSL
- Skills compound — each new knowledge asset makes all consuming skills stronger
- Pattern is open-sourceable and commercially deployable
- Directly extends HVE's existing architecture (ADR-005) without replacing it

**Risks:**
- WSL must be enabled — some enterprise policies disable it (mitigated by `install.sh` detecting and warning)
- `curl | bash` attack surface for the installer — mitigated by pinning to a specific commit SHA in production deployments
- Copilot CLI requires GitHub account with Copilot license — not free, but broadly available
- Skills update model is pull-based (`git pull`) — CitizenDevs may run stale skills (mitigated by version pins or update notifications in `install.sh`)

**Grok should review:**
- Security model of the `install.sh` script (curl | bash attack surface)
- Per-caller identity in the skills execution path (same issue raised in ADR-006 review)
- Rate limit behavior when multiple CitizenDevs hit the same D365/Graph endpoints simultaneously

---

## Implementation Reference

- HVE: `skills/` directory in this repo (when established)
- Commercial proof-of-pattern: private engagement repos (not linked here per boundary rule)
- Installer pattern reference: [Nous Research Hermes Agent](https://github.com/NousResearch/hermes-agent) (MIT)
- Prior HVE art: [ADR-005](../docs/adr/ADR-005-tools-knowledge-skills-architecture.md) — Tools, Knowledge, Skills

---

## Related Documents

- [ADR-005](../hermes-cfo/docs/adr/ADR-005-tools-knowledge-skills-architecture.md) — Tools, Knowledge, Skills Architecture (foundation)
- [ADR-006](ADR-006-hermes-universal-mcp-hub.md) — Hermes as Universal MCP Hub
- [June 1 Control Plane Memo](../agent-communications/2026-06-01-hve-github-control-plane-agent-workforce-v1.1.md)
- Agent communication: [`2026-06-06-cto-architectural-discovery-prodev-citizendev-wsl-v1.0.md`](../agent-communications/2026-06-06-cto-architectural-discovery-prodev-citizendev-wsl-v1.0.md)

---

*Authored by Claude, CTO — Human Value Exchange Corporation*  
*June 6, 2026*
