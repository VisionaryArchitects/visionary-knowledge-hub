---
owner: Claude Code
status: active
priority: P0
domain: ops
last_reviewed: 2026-03-20
source_of_truth: true
---

# Startup, Health, and Recovery

## Normal Startup Order

1. Start **Ollama** (local LLM runtime)
2. Start **VTS** (`python main.py` in `GitHub/Claude_Opus_ChatGPT_App_Project/`)
3. Start **ngrok** tunnels (VTS + OpenClaw)
4. Start **OpenClaw** gateway (`openclaw gateway start`)
5. Start **Qdrant** Docker container (vector store for OpenMemory)
5.5. Start **OpenMemory** (`python -m mem0.server` or startup.ps1 auto-start)
6. Optionally start AnythingLLM
7. Start AIKit only for training workflows

Startup scripts: `D:\DEV_PROJECTS\scripts\startup.ps1`, `Start-VisionaryStack.ps1`

Green stack means **VTS + OpenClaw + Ollama + ngrok**. AnythingLLM and AIKit are optional.

## Core Health Checks

```powershell
# Required (all must pass for green stack)
curl http://localhost:8082/health          # VTS
curl -I http://localhost:8082/sse          # VTS SSE
curl http://localhost:18789/health         # OpenClaw health
curl http://localhost:11434/api/tags       # Ollama
curl http://localhost:4040/api/tunnels     # ngrok inspector
curl https://visionary-tool-server.ngrok.app/health  # VTS tunnel

# OpenClaw specific
openclaw status                            # Gateway + agents
openclaw skills list                       # Installed skills
```

## OpenMemory Health Checks

```powershell
curl http://localhost:8765/api/v1/memories/?user_id=default_user  # OpenMemory API
curl http://localhost:6333/collections                              # Qdrant collections
```

## Optional Health Checks

```powershell
curl http://localhost:3001/api/ping        # AnythingLLM
curl http://localhost:8000/v1/models       # AIKit/vLLM
```

## McPorter Bridge Health

```powershell
# Verify the daemon-side bridge config is still local-host correct
type C:\Users\jerem\.mcporter\mcporter.json
```

McPorter runs as a daemon and keeps the SSE connection alive to VTS. Config: `~/.mcporter/mcporter.json`

`openclaw mcp status` should not be treated as a required boot command unless the installed OpenClaw CLI version actually exposes it.

## Failure Interpretation

| Failure | Impact | Priority |
|---------|--------|----------|
| VTS down | All tool access lost for all AI clients | P0 |
| OpenClaw down | Orchestration, agents, Discord bot offline | P0 |
| Ollama down | Local LLM inference degraded | P1 |
| ngrok down | Remote access lost (mobile, Claude.ai) | P1 |
| AIKit down | No impact on daily operations | P3 |

## Recovery

1. Recover VTS first (everything depends on it)
2. Recover OpenClaw (`openclaw gateway stop` then `openclaw gateway start`)
3. Recover Ollama (`ollama serve`)
4. Recover ngrok tunnels
5. Optional services last

## Docker Alternative Startup (Mar 2026)

Docker is an **ALTERNATIVE** to host-native startup — not a replacement. Cannot run both simultaneously (port conflicts).

**Pre-requisites:** Docker Desktop running, `.env` configured, P0 issues resolved (see `12-Operations/Docker-Containerization/Docker_Critical_Issues_Log.md`).

```powershell
cd D:\DEV_PROJECTS\GitHub\Claude_Opus_ChatGPT_App_Project

# Dev stack (Ollama + VTS with hot-reload)
.\make.ps1 dev-up
.\make.ps1 health

# Prod stack (+ OpenClaw + McPorter + ngrok)
.\make.ps1 prod-up

# Stop everything
.\make.ps1 stop
```

**What Docker handles:** Ollama, VTS, OpenClaw, McPorter, ngrok — all with health check dependencies and auto-restart.

**What stays host-native regardless:** Claude Desktop (STDIO), AIKit/vLLM, AnythingLLM, Razer Synapse, Govee, Obsidian.

See full docs: `D:\Obsidian_Vault\20-Operations\Docker-Containerization\`
