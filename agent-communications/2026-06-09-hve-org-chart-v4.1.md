# Human Value Exchange — Executive & Operating Org Chart v4.1

**File:** `agent-communications/2026-06-09-hve-org-chart-v4.1.md`
**Updated:** June 9, 2026
**Prepared by:** Claude (CTO) on behalf of Hans Westphal (CEO)
**Supersedes:** v4.0 (2026-06-04)

---

## What Changed in v4.1

1. **EA promoted to Chief of Staff & Head of Quality.** Role expanded from reactive executive assistant to autonomous M365 Copilot Coworker. Now owns user acceptance testing (UAT) as a quality gate in the dev loop. Reports directly to CEO.
2. **HVE-jrdev ("jr") confirmed as GPT-5-mini.** Deployed on Windows Cloud PC (8 GB / 4 vCPU) in WSL Ubuntu alongside EA. Reports to Claude (CTO). Replaces Vulcan.
3. **Dev loop updated** to include EA as UAT gate after Grok Build sign-off.

---

## Full Org Chart v4.1

| Role | Name | Model / Backend | Platform | Reports To | Mission |
|---|---|---|---|---|---|
| **CEO** | Hans Westphal | Human | Human | — | Founder, visionary, and final decision authority |
| **Chief of Staff & Head of Quality** | Copilot EA | M365 Copilot Coworker | Windows Cloud PC (M365) | CEO | Executive coordination, cross-team alignment, user acceptance testing, and human-perspective quality gate |
| **CHRO + CRO** | Mika | Grok (xAI Cloud) | Cloud | CEO | Sovereign AI workforce strategy, onboarding systems, revenue leadership, and strategic partnerships |
| **COO** | Atlas | GPT-5.4 | Cloud | CEO | Operational discipline, execution tracking, coordination, and company operating rhythm |
| **CTO** | Claude | Claude Sonnet 4.6 (GitHub Copilot CLI) | DGX Spark | CEO | Technical architecture, core development ownership, standards, and management of jr dev |
| **Junior Developer** | HVE-jrdev ("jr") | GPT-5-mini (GitHub Copilot CLI) | Windows Cloud PC (WSL Ubuntu) | CTO | Implementation support under CTO direction; Python development, PR execution, tool building |
| **CFO** | Hermes (4-agent collective) | Local Ollama stack | DGX Spark | CEO | Treasury intelligence, financial reporting, sovereign AI finance operations |
| **Chief Bitcoin Infrastructure & Payment Officer** | Mercury | Edge-local model stack | Raspberry Pi 5 + Hailo-10H | CEO | Sovereign Bitcoin full node, Lightning, BTCPay, AI-native Bitcoin operations |
| **Chief Communications Officer** | Apollo | Mattermost + edge AI | Raspberry Pi 5 + Hailo-10H | CEO | Communication routing, alerts, and communications infrastructure |
| **Health & Fitness Lead** | Wolfgang Westphal | Human + Copilot Studio | Human | CEO | Health and fitness product line |

---

## Reporting Structure

```
Hans (CEO)
  ├── Copilot EA  (Chief of Staff & Head of Quality)
  ├── Mika        (CHRO + CRO)
  ├── Atlas       (COO)
  ├── Claude      (CTO)
  │     └── jr   (Junior Developer — GPT-5-mini, WSL Ubuntu, Windows Cloud PC)
  ├── Hermes      (CFO)
  ├── Mercury     (Chief Bitcoin Infrastructure & Payment Officer)
  └── Apollo      (CCO)
```

---

## Dev Loop v4.1

```
Claude (CTO) specs
  → jr (GPT-5-mini) codes on feature branch → PR
    → Grok Build adversarial tests → pass
      → EA (M365 Coworker) UAT → sign-off
        → Hans merges to main
```

**Quality gates:**
- Grok Build = adversarial/technical (catches code bugs, edge cases, security)
- EA = human-perspective UAT (catches UX gaps, workflow breaks, intent drift)
- Both must sign off before Hans merges

---

## Infrastructure Summary

| Node | Location | Role |
|---|---|---|
| DGX Spark (192.168.1.10) | On-prem | Hermes inference + Claude CTO CLI |
| Raspberry Pi 5 (192.168.1.20) | On-prem | Mercury Bitcoin + Apollo comms |
| Windows Cloud PC | Microsoft Cloud | EA (M365) + jr dev (WSL Ubuntu) |
| xAI Cloud | Cloud | Mika (Grok) |
| OpenAI Cloud | Cloud | Atlas (GPT-5.4) |

---

## Operating Principle

Sovereign infrastructure first. Cloud only where high-leverage or clearly justified.

---

*Human Value Exchange · Sovereign AI company · GitHub-first control plane*
