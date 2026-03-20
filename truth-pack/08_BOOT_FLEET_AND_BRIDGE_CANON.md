---
owner: Codex CLI
status: active
priority: P0
domain: boot
last_reviewed: 2026-03-19
source_of_truth: true
---

# Boot Fleet and Bridge Canon

## Validated Boot Policy

- Runtime mode is **LOCAL host-first**, not Docker-first.
- Canonical boot docs live in `D:\Obsidian_Vault\11-Master-Docs\`.
- Captain-generated artifacts under `D:\DEV_PROJECTS\artifacts\codex-checkpoints\` are derived outputs, not the primary source of truth.
- Green boot means **VTS + OpenClaw + Ollama + ngrok** are healthy.
- **AnythingLLM** and **AIKit/vLLM** are optional and must not block a green boot.

## Active CLI Fleet

These are the enforced founder-boot wrappers:

| CLI | Status | Validation |
|-----|--------|------------|
| `codex` | active | `codex-cli 0.106.0-alpha.11` |
| `claude` | active | `2.1.79 (Claude Code)` |
| `gemini` | active | `0.34.0` |
| `copilot` | active | help command healthy |
| `opencode` | active | `1.2.17` |

Retired from enforced boot fleet: `qwen`, `grok`

## Founder Boot Requirements For Every CLI

Each wrapper must:

1. Run `Invoke-FounderBootPrimer.ps1`
2. Export Captain context artifacts
3. Inject a startup prompt that points the CLI to:
   - `BOOT_BRIEF.md`
   - `latest_working_set.md`
   - `latest.md`
   - `captain_context.md`
   - `FOUNDER_BOOT_PROTOCOL.md`
4. Set or inherit `MINIMAX_MODEL=MiniMax-M2.7`
5. Keep primary model routing unchanged

## MiniMax Sidecar Policy

- MiniMax remains a **sidecar planning/coding superpower**, not the primary runtime model for Codex CLI or OpenClaw.
- Default sidecar target is **`MiniMax-M2.7`**.
- Use MiniMax first for planning, research, and specialized code-analysis stages when available.

## OpenClaw Model Truth

Validated from `C:\Users\jerem\.openclaw\openclaw.json` and `openclaw status`:

- **BabyClaw main/default model**: `openai-codex/gpt-5.3-codex`
- **Agent defaults fallback**: `local-ollama/nemotron-3-nano:4b`
- **Forge/devops model**: `local-ollama/nemotron-3-nano:4b`
- **Watchtower/sentinel model**: `local-ollama/nemotron-3-nano:4b`
- **DeepScholar/research model**: `local-ollama/nemotron-3-nano:4b`

## OpenCode Canon

- Config path: `C:\Users\jerem\.config\opencode\opencode.json`
- Verified MCP entry:
  - `visionary-tool-server` -> `http://localhost:8082/sse`
- `MCP_DOCKER` may appear in `opencode mcp list` but is optional and currently timing out in local-host mode.

## Visionary AI Bridge

Treat the browser bridge as a documented, on-demand subsystem:

| Item | Value |
|------|-------|
| Product name | `Visionary AI Bridge` |
| Local path | `D:\DEV_PROJECTS\visionary-ai-bridge` |
| GitHub repo | `VisionaryArchitects/Visionary_GitHub_Agents` |
| Type | Chromium extension + native host + sidepanel |
| Boot policy | **On-demand validation only** |

It is not a hard dependency for a green CLI boot.

## Validation Commands

```powershell
codex --version
claude --version
gemini --version
copilot --help
opencode --version

curl http://localhost:8082/health
curl http://localhost:18789/health
curl http://localhost:11434/api/tags
curl http://localhost:4040/api/tunnels

openclaw status
opencode mcp list
```

## Drift Guards

- Do not regenerate boot artifacts from old `Visionary_Truth\MASTER_TRUTH_PACK` paths.
- Do not treat `openclaw mcp status` as a required check; current OpenClaw CLI does not expose that command.
- Do not require AnythingLLM or AIKit/vLLM for green boot.
