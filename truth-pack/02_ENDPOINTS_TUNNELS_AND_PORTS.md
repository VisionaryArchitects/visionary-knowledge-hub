---
owner: Claude Code
status: active
priority: P0
domain: endpoints
last_reviewed: 2026-03-20
source_of_truth: true
---

# Endpoints, Tunnels, and Ports

## Core Local Endpoints

| Service | URL | Purpose |
|---------|-----|---------|
| VTS Health | `http://localhost:8082/health` | Core health check |
| VTS SSE | `http://localhost:8082/sse` | MCP SSE connector (all AI clients) |
| OpenClaw Gateway | `ws://127.0.0.1:18789` | Agent orchestration gateway |
| OpenClaw Dashboard | `http://127.0.0.1:18789/` | Web dashboard |
| Ollama | `http://localhost:11434` | Local LLM runtime |
| Ollama Tags | `http://localhost:11434/api/tags` | Model availability check |
| AIKit (optional) | `http://localhost:8000/v1` | Training-only vLLM (OpenAI-compatible) |
| AnythingLLM | `http://localhost:3001` | Legacy local app |
| OpenMemory API | `http://localhost:8765` | Shared AI memory brain |
| OpenMemory MCP | `http://localhost:8765/mcp/{client}/sse/default_user` | Per-tool MCP SSE connector |
| Qdrant | `http://localhost:6333` | Vector store for OpenMemory (Docker) |

## ngrok Tunnels (Hobby Tier)

| Domain | Target | Notes |
|--------|--------|-------|
| `https://visionary-tool-server.ngrok.app` | `localhost:8082` | **Primary remote connector** |
| `https://visionary-openclaw.ngrok.app` | `localhost:18789` | OpenClaw remote access |
| `https://visionary-aikit.ngrok.app` | `localhost:3001` | Currently points to AnythingLLM, NOT AIKit |

**CRITICAL**: All domains use `.ngrok.app` (paid Hobby tier). NEVER use `.ngrok.io` — that's the old free tier.

## Port Map

| Port | Service | Required? |
|------|---------|-----------|
| 8082 | VTS | Yes |
| 18789 | OpenClaw | Yes |
| 11434 | Ollama | Yes |
| 4040 | ngrok inspector | Yes |
| 3001 | AnythingLLM | Optional |
| 8000 | AIKit/vLLM | Optional (training-only) |
| 8765 | OpenMemory | Yes (shared AI memory) |
| 6333 | Qdrant | Yes (OpenMemory vector store, Docker) |

## Primary Remote Connector

For any remote AI app needing MCP tool access:
```
https://visionary-tool-server.ngrok.app/sse
```
Requires: `main.py` running + `ngrok http 8082 --url=visionary-tool-server.ngrok.app`
