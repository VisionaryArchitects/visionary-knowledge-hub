---
owner: Claude Code
status: active
priority: P0
domain: rules
last_reviewed: 2026-03-16
source_of_truth: true
---

# Operating Rules and Drift Guards

## Identity

- **Owner**: BALLER (Jeremy, @VisionaryArchitects). Always address as BALLER.
- **Niche**: ADHD AI Tools, DevOps software, productivity programs
- **Vision**: Cross-device synchronicity (phone + keyboard + browser + terminal + website), shared session memory, self-employment via public marketplace

## Core Rules

1. **Never work around broken tools.** If a tool fails, FIX IT. Don't bypass it.
2. **Never take shortcuts.** BALLER catches them every time. Do it right or don't do it.
3. **Planning > Execution.** Get the plan perfect FIRST, then build.
4. **Let tools finish.** If MiniMax or any tool needs more time/tokens, increase limits. Don't substitute your own output.
5. **Fix tools systematically.** Debug step-by-step so the WHOLE stack works, not just a one-time workaround.
6. **Research first.** Every task: load context, search web, scan codebase, create plan, get approval.
7. **Verify before speaking.** Obsidian Vault is the main human-readable source of truth.

## Drift Guards (NEVER GET THESE WRONG)

| Fact | Correct Value | Common Drift |
|------|---------------|--------------|
| RTX 4090 VRAM | **16 GB** | Models assume 24 GB |
| ngrok domain | `.ngrok.app` | Old: `.ngrok.io` |
| Claude Desktop transport | **STDIO** via `main_stdio.py` | Never use SSE/mcp-remote |
| Claude.ai/Mobile | SSE via `visionary-tool-server.ngrok.app/sse` | Requires tunnel running |
| VTS version | **v5.0.0** | Old docs say v4.1.0 |
| VTS tools (core) | **152 tools, 19 modules** | Old docs say 96 tools |
| OpenClaw version | **v2026.3.13** | Old docs say 2026.2.21-2 |
| OpenClaw to VTS | **McPorter bridge** (daemon SSE) | NOT mcpServers in config |
| AIKit | **Training-only, optional** | Not part of green runtime |
| `visionary-aikit.ngrok.app` | Points to **AnythingLLM** | Not AIKit |
| Primary local LLM | **Ollama** v0.15.6 | Not AIKit/vLLM |
| VTS entry point | `GitHub/Claude_Opus_ChatGPT_App_Project/main.py` | NOT `razer-aikit-v2` |

## Communication Style

- BALLER is direct. Responds well to confidence and honesty.
- Calls out shortcuts immediately.
- Likes seeing progress and detailed output.
- Refers to AI tools as family ("big brother Claude Desktop", "BabyClaw" for OpenClaw).
- **NEVER tell BALLER to go to sleep or go to bed. Ever.**

## What To Tell Any AI App Up Front

```
Our live stack is VTS v5.0.0 + OpenClaw v2026.3.13 + Ollama v0.15.6 + ngrok.
Ollama is the primary local runtime (16 models, RTX 4090 16GB VRAM).
VTS provides 152 MCP tools via SSE on port 8082.
OpenClaw runs 4 agents (BabyClaw, Forge, Watchtower, DeepScholar) with 329 bridged VTS tools.
AIKit/vLLM is optional and training-only.
Use https://visionary-tool-server.ngrok.app/sse as the primary remote connector.
Always address the owner as BALLER.
```

## Anti-Patterns (DO NOT)

- Don't use placeholder code (`// ... rest of implementation`)
- Don't install packages on host OS (use dev container)
- Don't assume AIKit is required for green state
- Don't point general-purpose assistants at AIKit
- Don't modify MCP config files without explicit approval (they're verified working)
- Don't use `clawhub install --force` (it wipes before downloading — if download fails, everything is lost)
