# Human Value Exchange - GitHub as the Control Plane for the Agent Workforce

**Date:** June 1, 2026  
**Version:** 1.1 (supersedes the v1.0 draft circulated the same day)  
**From:** Hans Westphal, CEO  
**To:** Atlas (COO), Mika (CGO), Claude (Chief Architect), Hermes (CFO), Mercury, Apollo, Copilot EA, and all active agents  
**Status:** For adoption - open for comment through June 8, 2026 (see Request for Comment)

---

## Executive Summary

Human Value Exchange is formalizing a GitHub-first operating model for shared agent coordination, executive memory, work tracking, and decision history.

Effective immediately, GitHub becomes the primary control plane for the HVE agent workforce. Hermes, Mercury, and the rest of the executive agent team continue to operate in their own runtimes and environments, but the coordination, memory, review, and governance of shared work now default to GitHub.

This is a practical operating decision made to optimize for execution speed, durable memory, visible decisions, accountable collaboration, and a direct connection between discussion and implementation.

It also aligns HVE with the enterprise architecture pattern Microsoft is advancing through Agent 365, which Microsoft describes as "the control plane for AI agents" - a layer that deploys, organizes, secures, and governs agent fleets across Microsoft, open-source, and third-party platforms, giving each agent a managed identity.[1][2] The core idea is the separation of the execution plane (where agents do work) from the control plane (where agents are identified, observed, governed, and coordinated).[3]

Mattermost is not retired, but it is no longer the primary coordination surface for executive or cross-agent communication. It remains a supporting alert and bridge layer.

---

## The Architectural Decision

Human Value Exchange now operates with a two-plane architecture.

### 1. Execution Plane - where work happens

- Hermes - sovereign CFO and treasury agent
- Mercury - sovereign Bitcoin + Lightning operating node
- Claude, Atlas, Mika - session-bound executive agents using native interfaces
- Copilot EA - scheduling, support, and executive coordination

These systems generate outputs, perform actions, and execute business logic in their own runtimes.

### 2. Control Plane - where work is governed

GitHub is now the HVE control plane. It is used to coordinate shared work, preserve executive decisions, register proposals, disagreements, and approvals, maintain durable company memory, connect discussion directly to implementation, and provide a visible operating history of the company.

In Microsoft's model the control plane does not do the work itself; it governs how agents are identified, observed, secured, and managed. That separation is intentional and foundational[3] - and it is the same separation HVE adopts here, one tier below the enterprise scale Microsoft is selling into.

**Mercury and Hermes execute. GitHub governs. That is now the operating model.**

---

## The Generalizable Pattern - Why This Matters Beyond HVE

What we are building is not just HVE plumbing. It is a reference pattern - call it the **Sovereign Control-Plane Pattern** - and its defining property is that it is scale-invariant. The same five components hold whether the operator is a two-person studio or a Global 500 enterprise; only the implementation of each component changes:

| Component | Mom-and-pop / solo operator | Mid-market | Global 500 |
|---|---|---|---|
| Control plane | A single GitHub org + `agent-communications/` | GitHub org with branch protection + CODEOWNERS | GitHub Enterprise + Agent 365 / Entra Agent ID |
| Agent identity | Named GitHub accounts | GitHub Apps per agent | Entra-issued agent identities, governed in Defender/Purview/Intune[1] |
| Execution plane | A few session-bound agents | Mixed runtimes | Heterogeneous agent fleets across vendors[3] |
| Memory & audit | Issues, PRs, commit history | Same, + retention policy | Same, + compliance/eDiscovery |
| Source of record | "GitHub wins on conflict" rule | Enforced by policy | Enforced by platform controls |

The insight worth capturing: the cheapest version and the most regulated version are the same architecture. A solo founder can stand the pattern up in an afternoon with nothing but a GitHub org; an enterprise lands the identical shape on Agent 365 and Entra. That continuity - proven small, lifted large without re-architecting - is the unlock. HVE is the proof-of-pattern at the small end of a curve that terminates in Microsoft's top accounts.

---

## Boundary & Identity Separation (Non-Negotiable)

This control plane governs HVE only. Work performed for any external employer runs on that employer's own control plane, tenant, and identity. The two never share repositories, tenants, credentials, or agent identities.

This rule protects HVE, not the other party:

- **IP integrity.** Work product created on an employer's systems or time generally belongs to that employer. Keeping HVE's memory, decisions, and code exclusively under the `humanvalueexchange` GitHub org and HVE's own Microsoft tenant keeps the ownership line of our sovereign company clean and unambiguous.
- **Zero conflict surface.** Separate planes mean there is never a question about whether external resources funded, governed, or commingled with HVE work.

Operationally: HVE's control plane lives at `github.com/humanvalueexchange`. Any parallel engagement maintains its own, fully isolated instance of this same pattern.

---

## Why This Move Makes Sense Now

- **More pace.** GitHub removes hops between discussion and execution. Memo -> issue -> implementation -> merge is direct.
- **More memory.** Issues, comments, PRs, and commits preserve the real history of the company. Decisions no longer disappear into chat streams.
- **More visibility.** Approvals, objections, and design disagreements stay visible, improving accountability and reducing silent divergence.
- **Native alignment with Microsoft's agentic stack.** GitHub and GitHub Copilot CLI are among the strongest Microsoft-native surfaces for code-native agentic work, and the pattern lifts cleanly onto Agent 365's governance model as we scale.[1][3]
- **Practical sovereignty.** Our decisions, execution history, and institutional memory live in repositories we control, tied directly to the work itself.

---

## Operating Model

GitHub is the default home for durable coordination. Use GitHub first for anything that should be preserved, reviewed, linked to execution, visible across agents, or potentially publishable now or later.

### Primary GitHub surfaces

- `agent-communications/` - executive memos, policy statements, operating guidance, durable internal artifacts
- GitHub Issues - proposals, backlog, in-progress discussions, active debates, workstreams
- Issue comments - executive reviews, approvals, objections, refinements, operating instructions
- Pull requests - implementation review, merge audit trail, final design accountability

Mattermost is narrow-scope: real-time alerts, temporary bridge traffic from Mercury or Apollo, short-lived notifications, and fast coordination that is later made durable in GitHub. If a conversation produces a decision, policy, spec, or backlog item, the durable version belongs in GitHub the same day.

---

## Governance Principles (effective immediately)

1. **If it matters, put it on GitHub.**
2. **If it drives work, create or update an issue.**
3. **If it is a policy, publish it in `agent-communications/`.**
4. **If it is worth remembering, make it durable and linkable.**
5. **If Mattermost is used first, GitHub receives the durable version promptly.**
6. **If transient chat and a GitHub artifact disagree, GitHub is the source of record.**
7. **Merge authority is explicit.** `main` is branch-protected. A change to `agent-communications/` or to shared specs requires at least one approving review from a designated reviewer (default: Hans, with Atlas or Claude as standing delegates) before merge. Accountability that is not enforced in branch protection is only aspirational.

---

## Agent Identity and Participation

Each executive agent is a first-class participant in the HVE operating system, and participation must be identity-linked - not anonymous scripting. Concretely:

- Agent writes flow through GitHub Apps or service identities installed on the `humanvalueexchange` org, not through personal accounts. This gives each agent a revocable, repo-scoped, individually attributable identity in the audit trail.
- Naming is consistent and stable (Hermes, Mercury, Atlas, Mika, Claude, Apollo, Copilot EA), so issue participation, review comments, and PR approvals are attributable to a specific agent.

This mirrors the principle Microsoft now applies at enterprise scale: agents are managed digital entities with identity, visibility, and governance - in Agent 365, Entra-issued agent identities governed through Defender, Purview, and Intune - rather than isolated scripts or invisible tools.[1] HVE implements the small-footprint version of exactly that idea today, and the path to the enterprise version is additive, not a rebuild.

---

## Sovereignty & Portability

GitHub is less sovereign than a fully self-hosted communications fabric - but for a sovereignty-oriented company this pattern is stronger than it looks, because of one property: everything in the control plane is Markdown plus Git history, and Git is distributed by design.

That means the entire control plane - every memo, decision, issue thread, and commit - can be mirrored or fully migrated to self-hosted infrastructure such as Gitea or Forgejo at any time, with zero loss of memory or history. GitHub is therefore a convenience layer over a sovereign substrate, not a lock-in. We get GitHub's speed and tooling today while retaining an unconditional exit. For HVE, that is the most honest and durable form of sovereignty available in this phase: owned substrate, rented convenience, no hostage data.

---

## Strategic Outcome

This decision gives Human Value Exchange one durable memory layer, one visible coordination layer, one auditable decision history, and one Microsoft-friendly control plane for the agent workforce - implemented today in a way that scales, unchanged in shape, all the way to the enterprise tier.

---

## Immediate Next Steps

1. Finalize the `agent-communications/` repository structure under `github.com/humanvalueexchange`.
2. Define the issue taxonomy: `policy`, `feature`, `research`, `proposal`, `executive-review`.
3. Migrate active durable conversations into GitHub issues.
4. Stand up branch protection on `main` and a reviewer/CODEOWNERS rule (Principle 7).
5. Keep Mattermost for alerts and bridge notifications only.
6. Ensure all major design disagreements are captured explicitly in GitHub rather than resolved privately or transiently.

---

## Request for Comment

This memo is open for comment through June 8, 2026. Respond in GitHub - that is the point of the model.

Specifically:

- **Agree / adopt as-is:** add a one-line "adopted" comment under the tracking issue.
- **Amend:** open a comment proposing the specific edit, or a PR against this file.
- **Object:** open a comment labeled `objection` stating the concern and your proposed alternative. Silent divergence is the one failure mode this model exists to prevent.

Each agent is asked to confirm one concrete thing: the single workstream you will move into a GitHub issue first. That is how we prove the pattern with real traffic, not just assent.

---

## Closing

We are optimizing for execution, memory, and visible progress. For this phase of Human Value Exchange, the right move is simple:

**Build, discuss, decide, and document in GitHub first.**

**- Hans Westphal**  
CEO, Human Value Exchange

---

## Sources

1. Microsoft, "Microsoft Agent 365: The control plane for AI agents," Microsoft 365 Blog, Nov 18, 2025 - https://www.microsoft.com/en-us/microsoft-365/blog/2025/11/18/microsoft-agent-365-the-control-plane-for-ai-agents/
2. Microsoft, Agent 365 Resources (governance, registry, identity for enterprise-scale agents) - https://microsoft.github.io/agent-resources/agent365/
3. Industry reporting on Agent 365 general availability and the execution-vs-control-plane model, May 2026 - https://www.techtimes.com/articles/317458/20260531/microsoft-copilot-shifts-agent-governance-claude-checks-gpt-work-screen-agents-go-live.htm
4. Strategic framing support: M365 Copilot - Strategic Advisor
