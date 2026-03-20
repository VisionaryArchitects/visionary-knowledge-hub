---
owner: Codex CLI
status: active
priority: P0
domain: connections
last_reviewed: 2026-03-20
source_of_truth: true
---

# Connection Methods, Types, and Locations

This is the exact connection-method index for the live Visionary Architects
stack. Use it when another AI needs to know:

- what protocol each component uses
- which endpoint is local vs remote
- where the controlling config file lives
- which startup file or wrapper is responsible

## Core Runtime Connections

| Component | Method / Type | Local / Remote | Endpoint / Target | Config / Source |
|-----------|---------------|----------------|-------------------|-----------------|
| VTS health | HTTP GET | Local | `http://localhost:8082/health` | VTS runtime |
| VTS tool transport | MCP over SSE | Local | `http://localhost:8082/sse` | [main.py](D:/DEV_PROJECTS/GitHub/Claude_Opus_ChatGPT_App_Project/main.py) |
| VTS tool transport | MCP over SSE | Remote | `https://visionary-tool-server.ngrok.app/sse` | ngrok tunnel |
| OpenClaw gateway | WebSocket + token auth | Local | `ws://127.0.0.1:18789` | [openclaw.json](C:/Users/jerem/.openclaw/openclaw.json) |
| OpenClaw dashboard | HTTP | Local | `http://127.0.0.1:18789/` | OpenClaw gateway |
| OpenClaw health | HTTP GET | Local | `http://127.0.0.1:18789/health` | OpenClaw gateway |
| OpenClaw remote | HTTPS tunnel | Remote | `https://visionary-openclaw.ngrok.app` | ngrok tunnel |
| Ollama REST | HTTP | Local | `http://localhost:11434` | Ollama daemon |
| Ollama health/model list | HTTP GET | Local | `http://localhost:11434/api/tags` | Ollama daemon |
| Ollama OpenAI-compatible | OpenAI-compatible HTTP | Local | `http://localhost:11434/v1` | [openclaw.json](C:/Users/jerem/.openclaw/openclaw.json) |
| ngrok inspector | HTTP | Local | `http://localhost:4040` | ngrok desktop/agent |
| AIKit/vLLM | OpenAI-compatible HTTP | Local optional | `http://localhost:8000/v1` | optional training-only |
| AnythingLLM | HTTP | Local optional | `http://localhost:3001` | optional legacy app |

## AI Client Connection Map

| Client | Method / Type | Live Target | Config Path | Notes |
|--------|---------------|-------------|-------------|-------|
| Claude Desktop | STDIO process spawn | `main_stdio.py` | [claude_desktop_config.json](C:/Users/jerem/AppData/Roaming/Claude/claude_desktop_config.json) | Never SSE for Claude Desktop |
| Claude Code CLI | MCP via project config | `http://localhost:8082/sse` | [\.mcp.json](D:/DEV_PROJECTS/.mcp.json) | Also boots from Captain wrappers |
| Codex CLI | `mcp-remote` bridge to SSE | `http://localhost:8082/sse` | [config.toml](C:/Users/jerem/.codex/config.toml) | Global runtime model is `gpt-5.4` |
| Codex project extras | MCP config file | local SSE + MiniMax stdio | [mcp-config.json](D:/DEV_PROJECTS/.codex/mcp-config.json) | Adds `minimax-mcp` + `minimax-coding-plan-mcp` |
| GitHub Copilot CLI | MCP over SSE | `http://localhost:8082/sse` | [mcp-config.json](C:/Users/jerem/.copilot/mcp-config.json) | Must stay `type: sse` |
| Gemini CLI | MCP over SSE | `http://localhost:8082/sse` | [settings.json](C:/Users/jerem/.gemini/settings.json) | Also mounts `context7` and `kapture` |
| Gemini Antigravity | mixed MCP configs | separate toolset | [mcp_config.json](C:/Users/jerem/.gemini/antigravity/mcp_config.json) | Not the main founder boot path |
| OpenCode | remote MCP entry | `http://localhost:8082/sse` | [opencode.json](C:/Users/jerem/.config/opencode/opencode.json) | Also has optional `MCP_DOCKER` |
| ChatGPT Desktop | SSE connector | `https://visionary-tool-server.ngrok.app/sse` | ChatGPT UI settings | Remote client path |
| Claude.ai / mobile | SSE connector | `https://visionary-tool-server.ngrok.app/sse` | Claude UI connector | Requires ngrok |
| OpenClaw agents | McPorter bridge | local + remote VTS entries | [mcporter.json](C:/Users/jerem/.mcporter/mcporter.json) | OpenClaw itself should not use `mcpServers` for VTS |

## Important Config Files

| Purpose | Path |
|---------|------|
| Canon truth-pack root | `D:\Obsidian_Vault\11-Master-Docs\` |
| Founder boot canon | `D:\DEV_PROJECTS\CLI_BOOTSTRAP.md` |
| Captain boot scripts | `D:\DEV_PROJECTS\scripts\captain\` |
| Captain generated artifacts | `D:\DEV_PROJECTS\artifacts\codex-checkpoints\` |
| VTS entry point (SSE) | `D:\DEV_PROJECTS\GitHub\Claude_Opus_ChatGPT_App_Project\main.py` |
| VTS Claude Desktop entry point (STDIO) | `D:\DEV_PROJECTS\GitHub\Claude_Opus_ChatGPT_App_Project\main_stdio.py` |
| Codex global config | `C:\Users\jerem\.codex\config.toml` |
| Codex project MCP config | `D:\DEV_PROJECTS\.mcp.json` |
| Claude Desktop config | `C:\Users\jerem\AppData\Roaming\Claude\claude_desktop_config.json` |
| Copilot MCP config | `C:\Users\jerem\.copilot\mcp-config.json` |
| Gemini settings | `C:\Users\jerem\.gemini\settings.json` |
| OpenCode config | `C:\Users\jerem\.config\opencode\opencode.json` |
| OpenClaw config | `C:\Users\jerem\.openclaw\openclaw.json` |
| McPorter config | `C:\Users\jerem\.mcporter\mcporter.json` |
| OpenClaw paired devices | `C:\Users\jerem\.openclaw\devices\paired.json` |
| OpenClaw device auth | `C:\Users\jerem\.openclaw\identity\device-auth.json` |

## Boot and Startup Entry Points

| Purpose | Command / Script | Notes |
|---------|------------------|-------|
| Full stack startup | `D:\DEV_PROJECTS\scripts\startup.ps1` | Green boot = VTS + OpenClaw + Ollama + ngrok |
| Founder wrapper bootstrap | `D:\DEV_PROJECTS\scripts\captain\Start-AgentCLI.ps1` | Used by `codex`, `claude`, `gemini`, `copilot`, `opencode` wrappers |
| Founder primer | `D:\DEV_PROJECTS\scripts\captain\Invoke-FounderBootPrimer.ps1` | Loads read-first context and service checks |
| Codex artifact refresh | `D:\DEV_PROJECTS\scripts\captain\Start-CodexSession.ps1` | Regenerates checkpoint context |
| OpenClaw gateway service | `openclaw gateway start` | Scheduled task-backed on Windows |
| VTS server | `python main.py` in VTS repo | Uses SSE transport |

## ngrok Tunnel Map

| Tunnel Name | Public URL | Local Target | Status Role |
|-------------|------------|--------------|-------------|
| `vts` | `https://visionary-tool-server.ngrok.app` | `http://localhost:8082` | Primary remote MCP connector |
| `openclaw` | `https://visionary-openclaw.ngrok.app` | `http://localhost:18789` | OpenClaw remote access |
| `anythingllm` | `https://visionary-aikit.ngrok.app` | `http://localhost:3001` | Legacy/optional; not AIKit |

## MiniMax and Sidecar Tooling

| Item | Method / Type | Location | Notes |
|------|---------------|----------|-------|
| MiniMax sidecar model default | env/config value | `MINIMAX_MODEL=MiniMax-M2.7` | Current default for planning/coding sidecar |
| MiniMax MCP | stdio via `uvx` | [mcp-config.json](D:/DEV_PROJECTS/.codex/mcp-config.json) | `minimax-mcp@latest` |
| MiniMax Coding Plan MCP | stdio via `uvx` | [mcp-config.json](D:/DEV_PROJECTS/.codex/mcp-config.json) | `minimax-coding-plan-mcp@latest` |
| MiniMax VTS tools | native VTS tool module | [minimax.py](D:/DEV_PROJECTS/GitHub/Claude_Opus_ChatGPT_App_Project/app/tools/minimax.py) | Sidecar superpower path |

## Validation Commands

```powershell
curl http://localhost:8082/health
curl -I http://localhost:8082/sse
curl http://127.0.0.1:18789/health
curl http://localhost:11434/api/tags
curl http://localhost:4040/api/tunnels

Get-Content C:\Users\jerem\.codex\config.toml
Get-Content D:\DEV_PROJECTS\.mcp.json
Get-Content C:\Users\jerem\.copilot\mcp-config.json
Get-Content C:\Users\jerem\.gemini\settings.json
Get-Content C:\Users\jerem\.config\opencode\opencode.json
Get-Content C:\Users\jerem\.openclaw\openclaw.json
Get-Content C:\Users\jerem\.mcporter\mcporter.json
```

## Known Residual

- OpenClaw HTTP health can be green while some CLI-mode read commands remain
  degraded by the `operator.read` regression. Do not treat `openclaw status`
  as a stronger source of truth than `http://127.0.0.1:18789/health` when this
  regression is active.
