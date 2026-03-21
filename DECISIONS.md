# DECISIONS LOG — Visionary Architects
# APPEND-ONLY. Never delete or reorganize entries. Newest at bottom.

---
## 2026-03-20 — GitHub Repo as Canonical Source of Truth (Not Obsidian)
**Tool:** Claude Desktop (Dispatch/Cowork)
**Context:** Plans, decisions, and session context kept evaporating across 8+ AI tools. Obsidian plugins were auto-moving files. No single source of truth persisted reliably.
**Decision:** Create `visionary-knowledge-hub` GitHub repo as the single canonical source of truth for all AI tools.
**Rationale:** Every CLI tool already operates in git repos. Git provides version control, accessibility from every tool, and protection against accidental deletion. Obsidian becomes a read/write interface via sync, not the source.
**Alternatives Considered:** Obsidian vault (rejected — plugins auto-move files, local-only, half the tools can't write to it), Notion (rejected — external dependency, latency), local folder with no VCS (rejected — no history, no protection)
**Impact:** All context files (CLAUDE.md, AGENTS.md, GEMINI.md, copilot-instructions.md, SOUL.md) are generated from this repo. All session handoffs, decisions, and plans are logged here.
---

---
## 2026-03-19 — Local Host-First Mode (No Docker for OpenClaw)
**Tool:** Claude Desktop (Dispatch/Cowork)
**Context:** Docker mode for OpenClaw caused day-long debugging session that drained operator energy. Port conflicts, PATH issues, and config drift.
**Decision:** Run OpenClaw in local host-native mode, not Docker.
**Rationale:** Docker adds complexity without proportional benefit for a single-operator stack. Host-native is simpler, faster to debug, and already validated.
**Alternatives Considered:** Docker (rejected — too much friction for solo operator with ADHD)
**Impact:** Boot order uses `openclaw gateway start` directly. Docker compose files remain available as alternative but are not the default.
---

---
## 2026-03-19 — Node.js Reinstalled via winget (v24.14.0)
**Tool:** Claude Desktop (Dispatch/Cowork)
**Context:** Node.js was completely missing from PATH after Docker back-and-forth. OpenClaw gateway couldn't start.
**Decision:** Install Node.js LTS via `winget install OpenJS.NodeJS.LTS`. Note: truth pack says Volta-managed v25.6.1 — this needs reconciliation.
**Rationale:** Fastest path to unblock OpenClaw and all npm-based MCP servers.
**Alternatives Considered:** Volta reinstall (could be done later to restore managed version)
**Impact:** `node` available at v24.14.0. OpenClaw gateway starts successfully. All npm-based tools unblocked.
---


---
## 2026-03-20 — Tier 1 Productivity MCP Servers Mounted (Google, YouTube, MS365)
**Tool:** BabyClaw (OpenClaw/Discord)
**Context:** VTS Productivity Apps Integration Plan called for mounting Google Workspace, YouTube, MS365, and Home Assistant as Tier 1 MCP servers.
**Decision:** BabyClaw and BALLER mounted Google Workspace, YouTube, and Microsoft 365 MCP servers into VTS while Claude Desktop was running the folder cleanup.
**Rationale:** Highest-impact productivity integrations for daily driver apps.
**Alternatives Considered:** N/A — these were already planned.
**Impact:** VTS now has ~130+ additional productivity tools. Home Assistant remains pending.
---

---
## 2026-03-20 — Visionary Cockpit Will Ship as a WinUI Foundation Slice First
**Tool:** Codex CLI
**Context:** BALLER requested a flagship Windows app inspired by OpenClaw but expanded into a native Visionary Architects command shell with chat rail, adaptive canvas, and full-stack supervision.
**Decision:** Build `Visionary Cockpit` as a WinUI-native flagship shell, but execute it in a foundation-first slice: shell, Home workspace, Operations supervision, and seeded Projects/Knowledge entry points before deeper OpenClaw parity and live AI transport.
**Rationale:** This preserves the flagship architecture while keeping the first build tractable, testable, and aligned with the approved design/spec workflow.
**Alternatives Considered:** Exact OpenClaw clone first (rejected — too limiting for the flagship goal), full-everything v1 from day one (rejected — too broad to execute safely in one slice).
**Impact:** The approved implementation plan is `docs/superpowers/plans/2026-03-20-visionary-cockpit-foundation.md`. Execution is currently gated by WinUI environment readiness on the local machine.
---
