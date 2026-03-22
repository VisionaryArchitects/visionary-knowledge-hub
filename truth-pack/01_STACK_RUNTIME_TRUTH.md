---
owner: Claude Code
status: active
priority: P0
domain: runtime
last_reviewed: 2026-03-20
source_of_truth: true
---

# Stack Runtime Truth

## Executive Summary

The Visionary Architects production stack:

| Component | Version | Role |
|-----------|---------|------|
| **VTS** (Visionary Tool Server) | v5.0.0 | MCP tool spine — extended profile currently reports 262 loaded tools, 15 mounted servers |
| **OpenClaw** | v2026.3.13 | Agent orchestration gateway — 4 agents, Discord bot, cron jobs |
| **Ollama** | v0.15.6 | Primary local LLM runtime — 18 locally available models |
| **ngrok** | Hobby tier | Remote tunnel access — `.ngrok.app` domains (NOT `.ngrok.io`) |

## Hardware

| Spec | Value |
|------|-------|
| Machine | Razer Blade 16 |
| GPU | RTX 4090 Laptop — **16 GB VRAM** (NOT 24 GB) |
| RAM | 96 GB |
| OS | Windows 11 Home |
| Python | 3.14.2 |
| Node | v25.6.1 (system-installed; Volta removed Mar 18, 2026) |
| Storage | D: drive for all dev projects and models |

## VTS (Visionary Tool Server) v5.0.0

- **Framework**: FastMCP 2.14.2 + Starlette 0.50.0 + uvicorn 0.40.0
- **Protocol**: MCP v2024-11-05, SSE transport
- **Entry point**: `D:\DEV_PROJECTS\GitHub\Claude_Opus_ChatGPT_App_Project\main.py`
- **Tool modules**: `D:\DEV_PROJECTS\GitHub\Claude_Opus_ChatGPT_App_Project\app\tools\`
- **Profile system**: core (152 tools) / extended (~250+) / full (all 45 modules)
- **Current profile**: `extended` (set Mar 15, 2026)
- **Local SSE**: `http://localhost:8082/sse`
- **Remote SSE**: `https://visionary-tool-server.ngrok.app/sse`

## OpenClaw v2026.3.13

- **Gateway**: `ws://127.0.0.1:18789` (loopback-bound, token auth)
- **Dashboard**: `http://127.0.0.1:18789/`
- **Primary model**: `openai-codex/gpt-5.3-codex` (ChatGPT Pro OAuth)
- **Agent defaults fallback**: `local-ollama/nemotron-3-nano:4b`
- **Config**: `C:\Users\jerem\.openclaw\openclaw.json`
- **McPorter bridge**: v0.7.3 — bridges 329 VTS tools to all OpenClaw agents via daemon SSE

### Agents (4)

| Agent | Role | Model | Permissions |
|-------|------|-------|-------------|
| BabyClaw (Nova) | Main assistant | gpt-5.3-codex | Full (coding + messaging profiles) |
| Forge | DevOps | nemotron-3-nano:4b | Full (coding + messaging) |
| Watchtower | Sentinel | nemotron-3-nano:4b | Read-only (deny: write) |
| DeepScholar | Research | nemotron-3-nano:4b | Read-only (deny: write) |

### Cron Jobs (4)

| Job | Schedule |
|-----|----------|
| morning-briefing | 8 AM daily |
| health-scan | Every 15 minutes |
| security-audit | 2 AM daily |
| token-refresh | Mon/Thu 6 AM |

### Installed Skills

- **self-improving-agent** v3.0.4 — Learning, corrections, preference logging
- Hook `self-improvement` enabled (fires on `agent:bootstrap`)
- Workspace learnings: `D:\DEV_PROJECTS\.learnings\`

## Ollama v0.15.6

- **Endpoint**: `http://localhost:11434`
- **Model store**: `D:\visionary_models` (OLLAMA_MODELS env var)
- **Models**: 18 locally available (including `nemotron-3-nano:4b`, `qwen2.5:14b`, and the embedding stack)
- **Embedding models**: qwen3-embedding:8b (batch quality), qwen3-embedding:0.6b (daily driver)
- **Role**: All daily inference + embeddings. Dynamic model swapping, CPU fallback, 24/7.

## AIKit/vLLM (Optional)

- **Status**: Training-only, start on-demand
- **Endpoint**: `http://localhost:8000/v1` (OpenAI-compatible)
- **Model**: NVIDIA-Nemotron-Nano-9B-v2-FP8 (when loaded)
- **Use case**: LlamaFactory + LoRA fine-tuning on RTX 4090

## Green Runtime Definition

The stack is healthy when:
- VTS responds at `http://localhost:8082/health`
- OpenClaw gateway responds at `http://localhost:18789/health`
- Ollama responds at `http://localhost:11434/api/tags`
- VTS remote tunnel responds at `https://visionary-tool-server.ngrok.app/health`

AIKit being offline is NOT a failure.

## OpenMemory (Shared AI Brain)

- **Status**: Deployed natively (not Docker), auto-starts via startup.ps1 Step 5.5
- **API**: `http://localhost:8765`
- **Vector store**: Qdrant in Docker at `http://localhost:6333` (collection: `visionary_memory`, 1024 dims)
- **Embedding model**: `qwen3-embedding:0.6b` via Ollama (1024-dim vectors)
- **User ID**: `default_user`
- **MCP endpoint pattern**: `http://localhost:8765/mcp/{client-name}/sse/default_user`
- **Write mode**: Always use `infer: false` — AI tools write clean formatted text directly
- **Wired into**: All 6 CLI tools (Claude Code, Codex, Copilot, Gemini, OpenCode, Claude Desktop)
