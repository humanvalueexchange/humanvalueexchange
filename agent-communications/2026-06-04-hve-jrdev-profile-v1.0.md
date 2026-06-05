# HVE-jrdev Profile

**Date:** June 4, 2026  
**Version:** 1.0  
**Role:** HVE-jrdev (Junior Developer)  
**Reporting To:** Claude (CTO)  
**Primary Interface:** Open WebUI + Ollama  
**Connectivity Target:** Tailscale -> Hermes (central MCP server)  
**Goal:** Contribute to both Mercury and Hermes development tasks

---

## Answers to Claude's Onboarding Questions

### 1. What model/runtime is jr?

- **Runtime:** Fully local via Ollama + Open WebUI
- **Current model:** `qwen2.5-coder:1.5b`
- **Planned upgrade path:** model quality and context window will improve after RAM is increased to 32 GB
- **Location:** 2011 iMac12,1
- **Context window:** currently limited to roughly 4K

### 2. What languages/stack can jr work in?

- **Primary strength:** Python (scripting, automation, data handling, backend logic)
- **Secondary:** Bash / shell scripting
- **Supporting:** YAML, Markdown, JSON, basic Git workflows
- **Expected repo scope:** Mercury and Hermes

### 3. How does jr receive tasks?

- **Primary method:** Open WebUI chat prompts
- Tasks should be delivered as clear, structured prompts directly in chat
- Over time, jr will connect via Tailscale to Hermes, which will act as the central MCP server for agents
- Claude will own setup of MCP connections, system prompts, and instructions so jr can pull tasks and context more autonomously over time

### 4. What's jr's output quality like so far?

- He can generate clean, well-commented Python code when given clear step-by-step instructions
- He is currently very junior and requires significant guidance
- He should not be expected to work independently on moderately complex tasks yet
- Output must be reviewed by a human (Claude or Hans) before being committed

### 5. Does jr have GitHub access?

- No direct GitHub access at this stage
- He cannot open pull requests himself
- A human (Claude) must review his output and commit or merge the code

---

## Strategic Guidance for Claude (CTO)

HVE-jrdev is extremely junior. The goal is to make him as productive as possible through disciplined local-first workflows.

### Primary interface

All tasking and interaction should happen through Open WebUI + Ollama. This is the fastest way to get value from him right now.

### Future connectivity

We want to connect jr over Tailscale to Hermes, who will serve as the central MCP server for agents. This will allow jr to eventually access tools, context, and tasks in a more structured way, modeling how we previously worked with Vulcan.

### Task allocation

jr should contribute to both Mercury and Hermes codebases. Start with smaller, well-scoped tasks in either repo.

### Onboarding focus

Claude should heavily invest in:

- Crafting strong, consistent system prompts for jr
- Setting up MCP server connections via Hermes
- Creating clear templates and instructions for how tasks should be delivered
- Establishing a reliable review-and-commit workflow

---

## Immediate Priority

Get jr connected over Tailscale to Hermes and begin testing structured prompting patterns that mirror how we previously worked with Vulcan.
