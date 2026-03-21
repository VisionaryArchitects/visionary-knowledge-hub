# ACTIVE PLANS — Visionary Architects
# APPEND-ONLY for new plans. Update status inline for existing plans. Newest at bottom.

---
## VTS Productivity Apps Integration — Status: ACTIVE
**Created:** 2026-03-19 by Claude Desktop (Dispatch)
**Last Updated:** 2026-03-19 by Claude Desktop (Dispatch)
**Owner:** BALLER
**Summary:** Wire Google Workspace, Microsoft 365, Home Assistant, YouTube, and Spotify into VTS as MCP mounts so BabyClaw becomes a real productivity assistant.
**Phases:**
1. Fix Node.js PATH — COMPLETED (v24.14.0 installed)
2. Mount Google Workspace MCP (~100 tools) — PENDING (needs OAuth setup)
3. Mount Home Assistant MCP (~15 tools) — PENDING (needs HA long-lived token)
4. Mount YouTube MCP (~10 tools) — PENDING (needs API key)
5. Mount Microsoft 365 MCP (~20 tools) — PENDING (needs Azure AD app)
6. Mount Spotify MCP (~10 tools) — PENDING (optional, lower priority)
**Blockers:** None after Node.js fix
**Related Docs:** `truth-pack/01_STACK_RUNTIME_TRUTH.md`, Obsidian `10-Projects/VTS_PRODUCTIVITY_APPS_INTEGRATION_PLAN.md`
---

---
## Unified Knowledge Sync Architecture — Status: ACTIVE
**Created:** 2026-03-20 by Claude Desktop (Dispatch)
**Last Updated:** 2026-03-20 by Claude Desktop (Dispatch)
**Owner:** BALLER
**Summary:** Create a 3-layer architecture (GitHub repo + OpenMemory MCP + Session Handoff Protocol) to eliminate doc drift and session knowledge loss across 8+ AI tools.
**Phases:**
1. Create visionary-knowledge-hub GitHub repo — COMPLETED
2. Seed with truth pack + context files — IN PROGRESS
3. Deploy OpenMemory MCP (shared semantic memory) — PENDING
4. Wire sync scripts to update local copies on boot — PENDING
5. Add handoff protocol to all tool context files — IN PROGRESS
6. Obsidian bidirectional sync — PENDING
**Blockers:** None
**Related Docs:** Obsidian `10-Projects/UNIFIED_KNOWLEDGE_SYNC_ARCHITECTURE.md`
---

---
## BabyClaw Config Fix (7 Changes) — Status: PENDING
**Created:** 2026-03-18 by Claude Code CLI
**Last Updated:** 2026-03-19 by Claude Desktop (Dispatch)
**Owner:** BALLER
**Summary:** Apply 7 config changes to openclaw.json to uncripple BabyClaw's tool access — remove group:automation deny, fix exec security to allowlist, enable Sentinel exec, etc.
**Phases:**
1. Backup openclaw.json — PENDING
2. Apply 4 active changes — PENDING
3. Verify 3 no-op checks — PENDING
4. Restart gateway and test — PENDING
**Blockers:** None (gateway is live, config file accessible)
**Related Docs:** Obsidian `Prompts/BABYCLAW_CONFIG_FIX_HANDOFF.md`
---

---
## Visionary Cockpit Foundation — Status: BLOCKED
**Created:** 2026-03-20 by Codex CLI
**Last Updated:** 2026-03-20 by Codex CLI
**Owner:** BALLER
**Summary:** Build the first WinUI flagship foundation for Visionary Cockpit: native shell, left chat rail, adaptive right canvas, and operations supervision for VTS/OpenClaw/Ollama/ngrok.
**Phases:**
1. Non-mutating WinUI readiness audit — COMPLETED
2. Scaffold unpackaged WinUI foundation + repo SDK/TFM pin — PENDING
3. Ship shell/navigation foundation — PENDING
4. Ship Home workspace with persistence — PENDING
5. Ship Operations supervision foundation — PENDING
6. Ship Projects + Knowledge entry points — PENDING
7. Run flagship foundation verification pass — PENDING
**Blockers:** Current machine does not have the `winui` template installed; host-level bootstrap/install path is not allowed without explicit approval override.
**Related Docs:** `docs/superpowers/specs/2026-03-20-visionary-cockpit-design.md`, `docs/superpowers/plans/2026-03-20-visionary-cockpit-foundation.md`
---
