---
owner: Codex CLI
status: active
priority: P0
domain: handoff
last_reviewed: 2026-03-20
source_of_truth: true
---

# AI Context Recap and Handoff

This is the dense handoff file for another AI. Read this after `01_STACK_RUNTIME_TRUTH.md`
if you need the fastest accurate briefing.

## Executive Summary

- Owner is **BALLER**
- Runtime mode is **LOCAL host-first**
- Canon truth-pack location is `D:\Obsidian_Vault\11-Master-Docs\`
- Green stack is **VTS + OpenClaw + Ollama + ngrok**
- `AIKit/vLLM` is optional and training-only
- MiniMax remains a **sidecar superpower**, not the primary model runtime

## What Is Live Right Now

- **VTS**: `v5.0.0`, SSE transport, `extended` profile
- **VTS live health**: `http://localhost:8082/health`
- **VTS live SSE**: `http://localhost:8082/sse`
- **VTS remote SSE**: `https://visionary-tool-server.ngrok.app/sse`
- **OpenClaw**: `v2026.3.13`, local HTTP health is live at `http://127.0.0.1:18789/health`
- **Ollama**: `v0.15.6`, live at `http://localhost:11434`
- **ngrok**: hobby-tier `.ngrok.app` tunnels are active
- **Node**: `v25.6.1`
- **Python**: `3.14.2`

## What Another AI Must Not Get Wrong

- RTX 4090 laptop has **16 GB VRAM**, not 24 GB
- Use `.ngrok.app`, never `.ngrok.io`
- Canon docs are under `11-Master-Docs`, not the old `Visionary_Truth\MASTER_TRUTH_PACK` path
- Claude Desktop uses **STDIO** through `main_stdio.py`
- OpenClaw talks to VTS through **McPorter**, not through `mcpServers` in `openclaw.json`
- `visionary-aikit.ngrok.app` currently fronts **AnythingLLM**, not AIKit
- `AIKit/vLLM` being offline is **not** a green-stack failure

## Read First Order For Another AI

1. [00_README_MASTER_TRUTH_PACK.md](D:/Obsidian_Vault/11-Master-Docs/00_README_MASTER_TRUTH_PACK.md)
2. [01_STACK_RUNTIME_TRUTH.md](D:/Obsidian_Vault/11-Master-Docs/01_STACK_RUNTIME_TRUTH.md)
3. [03_STARTUP_HEALTH_AND_RECOVERY.md](D:/Obsidian_Vault/11-Master-Docs/03_STARTUP_HEALTH_AND_RECOVERY.md)
4. [06_OPERATING_RULES_AND_DRIFT_GUARDS.md](D:/Obsidian_Vault/11-Master-Docs/06_OPERATING_RULES_AND_DRIFT_GUARDS.md)
5. [07_BEHAVIORAL_STACK_AND_AGENTS.md](D:/Obsidian_Vault/11-Master-Docs/07_BEHAVIORAL_STACK_AND_AGENTS.md)
6. [08_BOOT_FLEET_AND_BRIDGE_CANON.md](D:/Obsidian_Vault/11-Master-Docs/08_BOOT_FLEET_AND_BRIDGE_CANON.md)
7. [09_CONNECTION_METHODS_TYPES_AND_LOCATIONS.md](D:/Obsidian_Vault/11-Master-Docs/09_CONNECTION_METHODS_TYPES_AND_LOCATIONS.md)

## Active AI Fleet

These are the enforced founder-boot CLIs:

- `codex`
- `claude`
- `gemini`
- `copilot`
- `opencode`

Retired from enforced boot fleet:

- `qwen`
- `grok`

## Model Truth

- **Codex CLI primary model**: `gpt-5.4`
- **OpenClaw main/BabyClaw model**: `gpt-5.3-codex`
- **OpenClaw non-main agent runtime**: `nemotron-3-nano:4b`
- **MiniMax sidecar default**: `MiniMax-M2.7`
- **Ollama primary local inference**: live and preferred for local small/medium models
- **AIKit/vLLM**: optional `NVIDIA-Nemotron-Nano-9B-v2-FP8` path for training/on-demand use

## Connection Truth

- Most local CLI clients should prefer `http://localhost:8082/sse`
- Remote tools should prefer `https://visionary-tool-server.ngrok.app/sse`
- Claude Desktop should spawn `main_stdio.py` directly
- OpenClaw should use McPorter:
  - local VTS: `http://localhost:8082/sse`
  - remote VTS: `https://visionary-tool-server.ngrok.app/sse`

## Boot Truth

- Founder boot is controlled by:
  - [CLI_BOOTSTRAP.md](D:/DEV_PROJECTS/CLI_BOOTSTRAP.md)
  - [Start-AgentCLI.ps1](D:/DEV_PROJECTS/scripts/captain/Start-AgentCLI.ps1)
  - [Invoke-FounderBootPrimer.ps1](D:/DEV_PROJECTS/scripts/captain/Invoke-FounderBootPrimer.ps1)
- Captain artifacts are generated under:
  - `D:\DEV_PROJECTS\artifacts\codex-checkpoints\`
- Read-first generated artifacts:
  - [BOOT_BRIEF.md](D:/DEV_PROJECTS/artifacts/codex-checkpoints/BOOT_BRIEF.md)
  - [latest_working_set.md](D:/DEV_PROJECTS/artifacts/codex-checkpoints/latest_working_set.md)
  - [latest.md](D:/DEV_PROJECTS/artifacts/codex-checkpoints/latest.md)
  - [captain_context.md](D:/DEV_PROJECTS/artifacts/codex-checkpoints/captain_context.md)

## Visionary AI Bridge Truth

- Product/system name: `Visionary AI Bridge`
- Local folder: `D:\DEV_PROJECTS\visionary-ai-bridge`
- GitHub repo: `VisionaryArchitects/Visionary_GitHub_Agents`
- Role: Chromium extension + native host + sidepanel + CLI/browser handoff
- Policy: documented and validated **on demand**, not a hard green-boot dependency

## Current Residuals

- OpenClaw local HTTP health is live, but some CLI-mode read commands remain degraded:
  - `openclaw status`
  - `openclaw gateway probe`
  - `openclaw devices list`
- That is part of the known `operator.read` regression pattern already documented locally
- Do not confuse that regression with total gateway outage

## High-Value Commands To Verify Before Speaking

```powershell
curl http://localhost:8082/health
curl -I http://localhost:8082/sse
curl http://127.0.0.1:18789/health
curl http://localhost:11434/api/tags
curl http://localhost:4040/api/tunnels
Get-Content D:\DEV_PROJECTS\.mcp.json
Get-Content C:\Users\jerem\.codex\config.toml
Get-Content C:\Users\jerem\.openclaw\openclaw.json
Get-Content C:\Users\jerem\.mcporter\mcporter.json
```

## Best Short Summary For Another AI

Visionary Architects is currently a local-host-first stack centered on VTS as
the tool spine, OpenClaw as the agent gateway, Ollama as the primary local LLM
runtime, and ngrok as the remote connector layer. The authoritative docs live
in `D:\Obsidian_Vault\11-Master-Docs\`. Booted CLI agents should read the
captain artifacts, use MiniMax as a sidecar planning/coding superpower with
`MiniMax-M2.7`, keep Codex/OpenClaw primary model routing unchanged, and verify
live endpoints before making claims.
