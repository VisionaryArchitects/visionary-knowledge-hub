---
owner: Claude Code
status: active
priority: P0
domain: connectors
last_reviewed: 2026-03-16
source_of_truth: true
---

# Connector and Client Map

## How Each AI Client Connects to VTS

| Client | Transport | Config / Endpoint |
|--------|-----------|-------------------|
| **Claude Desktop** | **STDIO** (spawns process) | `main_stdio.py` — NEVER use SSE or mcp-remote |
| **Claude Code CLI** | SSE (local) | `http://localhost:8082/sse` via `.mcp.json` |
| **Claude.ai / Mobile** | SSE (remote) | `https://visionary-tool-server.ngrok.app/sse` |
| **ChatGPT Desktop** | SSE Connector | `https://visionary-tool-server.ngrok.app/sse` |
| **GitHub Copilot CLI** | SSE (local) | `http://localhost:8082/sse` via `.copilot/mcp-config.json` |
| **Gemini CLI** | SSE (local) | `http://localhost:8082/sse` via `.gemini/settings.json` |
| **Codex CLI** | SSE (local) | `http://localhost:8082/sse` via `.codex/mcp-config.json` |
| **OpenCode** | SSE (local) | `http://localhost:8082/sse` via `C:\Users\jerem\.config\opencode\opencode.json` |
| **Perplexity Spaces** | Upload docs | Upload this truth pack as context files |
| **OpenClaw agents** | McPorter bridge | Daemon SSE to VTS (329 tools bridged) |

## MCP Server Counts Per Client

| Client | MCP Servers |
|--------|-------------|
| Claude Code CLI | 16 |
| Codex CLI | 18 |
| Copilot CLI | 15 |
| Gemini CLI | 15 |
| OpenCode | 1 required (`visionary-tool-server`) |
| Claude Desktop | 14 |

## Critical Rules

1. **Claude Desktop** uses STDIO (`main_stdio.py`), NOT SSE. It spawns the process directly. NEVER use mcp-remote for Claude Desktop.
2. **Copilot CLI** MUST use `"type": "sse"` with `"url"`. NEVER `"type": "http"` with `"endpoint"`.
3. **main.py** transport line MUST be `app = mcp.http_app(transport='sse')` — NOT `transport='http'` or default.
4. **Remote clients** (Claude.ai, ChatGPT, mobile) require ngrok tunnel running.
5. **OpenClaw** connects to VTS via McPorter daemon, NOT via `mcpServers` in openclaw.json.
6. **OpenCode** is part of the active CLI boot fleet and should point at local VTS SSE.

## For New AI Apps

When setting up a new AI app with VTS access:
- **If it supports MCP/SSE**: Use `https://visionary-tool-server.ngrok.app/sse`
- **If it supports file upload**: Upload this truth pack (docs 01-06)
- **If it supports custom instructions**: Paste content from `06_OPERATING_RULES_AND_DRIFT_GUARDS.md`
