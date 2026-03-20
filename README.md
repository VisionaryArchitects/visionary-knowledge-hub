# Visionary Knowledge Hub

**The single source of truth for all Visionary Architects AI tools.**

This repo eliminates doc drift and session knowledge loss across 8+ AI tools by providing:

1. **One canonical truth pack** (`truth-pack/`) — Stack runtime, endpoints, connections, boot order, drift guards
2. **Tool-specific context files** — `CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, `copilot-instructions.md`, `SOUL.md` — each auto-read by their respective CLI tool, all generated from the same source
3. **Append-only logs** — `DECISIONS.md`, `ACTIVE_PLANS.md`, `SESSION_LOG.md` — every AI writes here, every AI reads here
4. **Hard-coded session handoff protocol** — baked into every context file, non-negotiable

## How It Works

Every AI tool BALLER uses reads a context file on boot:
- **Claude Code CLI** reads `CLAUDE.md`
- **Codex CLI (GPT 5.4)** reads `AGENTS.md`
- **Gemini CLI** reads `GEMINI.md`
- **GitHub Copilot CLI** reads `.github/copilot-instructions.md`
- **BabyClaw (OpenClaw)** reads `SOUL.md`

All five files contain the same core truth + a mandatory session handoff protocol that requires logging decisions, plans, and state changes to the append-only files.

## Repo Structure

```
visionary-knowledge-hub/
├── CLAUDE.md                    # Claude Code CLI context
├── AGENTS.md                    # Codex CLI context
├── GEMINI.md                    # Gemini CLI context
├── SOUL.md                      # BabyClaw/OpenClaw context
├── .github/
│   └── copilot-instructions.md  # Copilot CLI context
├── SESSION_HANDOFF_PROTOCOL.md  # The rules every AI must follow
├── DECISIONS.md                 # Append-only decision log
├── ACTIVE_PLANS.md              # Current plans and their status
├── SESSION_LOG.md               # Session handoff entries from all tools
└── truth-pack/                  # Canonical stack documentation (10 docs)
    ├── 00_README_MASTER_TRUTH_PACK.md
    ├── 01_STACK_RUNTIME_TRUTH.md
    ├── 02_ENDPOINTS_TUNNELS_AND_PORTS.md
    ├── 03_STARTUP_HEALTH_AND_RECOVERY.md
    ├── 04_CONNECTOR_AND_CLIENT_MAP.md
    ├── 05_PROJECTS_REPOS_AND_PATHS.md
    ├── 06_OPERATING_RULES_AND_DRIFT_GUARDS.md
    ├── 07_BEHAVIORAL_STACK_AND_AGENTS.md
    ├── 08_BOOT_FLEET_AND_BRIDGE_CANON.md
    ├── 09_CONNECTION_METHODS_TYPES_AND_LOCATIONS.md
    └── 10_AI_CONTEXT_RECAP_AND_HANDOFF.md
```

## Rules

- **This repo is the canonical source.** If it conflicts with Obsidian, a planning note, or an old doc — this repo wins.
- **Context files are generated.** Don't edit CLAUDE.md/AGENTS.md/GEMINI.md directly. Edit truth-pack/ docs.
- **Logs are append-only.** Never delete, reorder, or reorganize entries in DECISIONS.md, ACTIVE_PLANS.md, or SESSION_LOG.md.
- **Every AI must follow the handoff protocol.** No exceptions.

## Owner

BALLER (Jeremy, @VisionaryArchitects)
