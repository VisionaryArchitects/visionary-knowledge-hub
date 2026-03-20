---
owner: Claude Code
status: active
priority: P0
domain: behavioral
last_reviewed: 2026-03-16
source_of_truth: true
---

# Behavioral Stack and Agent Architecture

## Philosophy

VTS handles all **tool execution** (152 MCP tools + 15 mounted servers = 329 tools via McPorter bridge).
Behavioral skills handle how agents **think, learn, and act** — self-improvement, proactivity, security governance, and evolution.

This separation means: VTS = the hands, behavioral skills = the brain patterns.

Current runtime mode is **LOCAL host-first** after the March 18, 2026 Docker rollback. Use `localhost` endpoints, not Docker DNS names.

## Installed Behavioral Skills (4)

| Skill | Version | Role | Source |
|-------|---------|------|--------|
| **self-improving-agent** | 3.0.4 | Learning, corrections, preference logging | ClawHub |
| **proactive-agent-lite** | 1.0.0 | Proactivity, reverse prompting, self-healing | ClawHub |
| **capability-evolver** | 1.31.0 | Self-evolution engine, runtime history analysis | ClawHub |
| **skill-vetter** | 1.0.0 | Security vetting before installing any skill | ClawHub |

All installed at `C:\Users\jerem\.openclaw\skills\` (openclaw-managed source).

### self-improving-agent (Learning Engine)
- **Hook**: `self-improvement` fires on `agent:bootstrap`
- **Workspace**: `D:\DEV_PROJECTS\.learnings\` (LEARNINGS.md, ERRORS.md, FEATURE_REQUESTS.md)
- **Write access**: BabyClaw + Forge only. Watchtower + DeepScholar are read-only.
- **Promotion**: Patterns appearing 3+ times over 30 days auto-promote to SOUL.md / AGENTS.md / TOOLS.md

### proactive-agent-lite (Initiative Brain)
- Converts agents from reactive to proactive
- Memory pre-compaction flush (context survival)
- Reverse prompting (surfaces ideas you didn't ask for)
- Self-healing patterns (auto-diagnose and fix issues)

### capability-evolver (Evolution Engine)
- GEP (Genetic Evolution Protocol) — runtime analysis with protocol-constrained evolution
- A2A integration for multi-agent coordination
- Memory graph + personality modeling
- Active development (v1.31.0, 72 files, updated daily)

### skill-vetter (Security Governance)
- Mandatory vetting before installing ANY skill
- Red flag detection (data exfil, credential access, obfuscated code)
- Risk classification: LOW / MEDIUM / HIGH / EXTREME
- Produces structured vetting reports

## Agent Configuration (4 Agents)

| Agent | Name | Role | Model | Tools Access |
|-------|------|------|-------|--------------|
| BabyClaw | Nova | Main assistant | gpt-5.3-codex | Full VTS (329 tools via McPorter) |
| Forge | — | DevOps | nemotron-3-nano:4b | Full VTS (329 tools) |
| Watchtower | — | Sentinel | nemotron-3-nano:4b | Read-only (deny: write) |
| DeepScholar | — | Research | nemotron-3-nano:4b | Read-only (deny: write) |

## OpenClaw Model Policy

- **Primary/default main agent**: `openai-codex/gpt-5.3-codex`
- **Agent defaults fallback**: `local-ollama/nemotron-3-nano:4b`
- **Non-main custom agent set**: local Nemotron Nano 4B agentic models via Ollama

## Cron Jobs (4 Active)

| Job | Schedule | Purpose |
|-----|----------|---------|
| morning-briefing | 8 AM daily | Daily briefing to Discord |
| health-scan | Every 15 min | Stack health monitoring |
| security-audit | 2 AM daily | Nightly security scan |
| token-refresh | Mon/Thu 6 AM | OAuth token rotation |

## Behavioral Governance Rules

### Session Isolation
- Each agent session is isolated — no cross-contamination of context
- Sandbox mode: `all` (all executions sandboxed)
- Sub-agent exec approval: enabled (`on-miss`)

### Agent Permissions
- BabyClaw + Forge: coding + messaging profiles (full write)
- Watchtower + DeepScholar: deny write (read-only sentinels)
- All agents: VTS tools accessible via McPorter bridge

### Skill Installation Rules
1. ALWAYS run skill-vetter before installing new skills
2. NEVER use `clawhub install --force` (wipes directory before downloading — if download fails, everything is lost)
3. If rate-limited, use `clawhub inspect <slug> --file <path>` workaround to pull individual files
4. Manually create lockfile + origin.json entries after manual installs

## Memory Architecture

- **Embeddings**: Ollama + qwen3-embedding:0.6b (daily driver)
- **Index**: 1058 files, 5531 chunks, 36K cached embeddings
- **Provider**: Native `"ollama"` (NOT OpenAI compat layer)
- **Fallback**: `"ollama"` (NOT `"none"`)
- **Known limitation**: Batch embedding timeout at 120s (hardcoded in OpenClaw). Individual queries work (1.4s). FTS index available for keyword searches.

## Behavioral Patterns (From Self-Improving + Proactive Stack)

1. **Learning loop**: Errors and corrections logged to `.learnings/` → patterns promoted to SOUL.md after 3+ occurrences
2. **Proactive suggestions**: Agent surfaces ideas without being asked (reverse prompting)
3. **Self-healing**: Agent detects and fixes its own issues automatically
4. **Security vetting**: New skills vetted before installation
5. **Capability evolution**: Runtime history analyzed for improvement opportunities

## What's NOT Installed (Evaluated and Skipped)

| Skill | Why Skipped |
|-------|-------------|
| automation-workflows | Solopreneur business tool (Zapier/Make/n8n) — not agent behavioral |
| openclaw-mission-control | macOS-native dashboard — we're on Windows |
| memory-setup | We already have memory working + self-improving-agent handles behavioral memory |

## Future Behavioral Upgrades

- **Scheduled workflow orchestration**: Chain behavioral skills with cron for automated routines
- **Activity feed**: Log all agent actions for behavior review
- **Progress reporting**: Structured progress every 3 min during long tasks
- **Concurrency control**: Prevent resource stampedes during parallel agent work
