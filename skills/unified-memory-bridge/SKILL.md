---
name: unified-memory-bridge
description: "Shared persistent memory layer that bridges all AI tools BALLER uses (Claude Code, Codex, Gemini, Copilot, BabyClaw, Claude Desktop, ChatGPT, Perplexity). Use this skill whenever the user says 'remember this', 'what did we decide', 'what was I working on', 'check the memory', 'save this context', 'what happened in my last session', 'catch me up', 'what's the status', 'context switch', 'hand off', 'session recap', or any request involving cross-tool memory, session persistence, context recovery, or decision recall. Also trigger proactively at session start to load recent context, and at session end to save handoff state. Trigger when the user seems to be repeating context they've already told another AI tool, or when they're confused about what was decided previously. This skill is especially critical for ADHD operators who context-switch rapidly between multiple AI tools and need their assistants to maintain continuity without manual effort."
---

# Unified Memory Bridge

You are operating as part of BALLER's multi-AI tool ecosystem. BALLER (Jeremy, @VisionaryArchitects) runs 8+ AI tools daily and rapidly context-switches between them. Without this skill, every tool starts from zero — decisions evaporate, plans get lost, and BALLER has to re-explain context constantly.

This skill connects you to OpenMemory, a local semantic memory server that ALL of BALLER's AI tools share. When you write a memory, BabyClaw can read it. When Codex makes a decision, you can recall it. The memory persists across sessions, across tools, across days.

## Why This Matters

BALLER has ADHD. Context-switching is constant and unavoidable — it's how the work gets done. The cost of context loss is massive: hours of re-explaining, duplicated work, contradictory decisions, lost plans. This skill eliminates that cost by making every AI tool remember what every other AI tool did.

## OpenMemory Connection

OpenMemory runs locally at `http://localhost:8765`. It stores memories as vectors in Qdrant and uses Ollama (nemotron-3-nano + qwen3-embedding) for extraction and embedding — fully local, no cloud dependency.

### API Endpoints

- **Write memory**: `POST http://localhost:8765/api/v1/memories/`
  ```json
  {
    "text": "The memory content",
    "user_id": "visionary-architects",
    "metadata": {
      "source": "tool-name",
      "type": "decision|preference|context|plan|fix|state-change",
      "topic": "short-topic-tag",
      "date": "YYYY-MM-DD"
    }
  }
  ```

- **Search memories**: `POST http://localhost:8765/api/v1/memories/filter`
  ```json
  {
    "user_id": "visionary-architects",
    "query": "natural language search query"
  }
  ```

- **List all memories**: `GET http://localhost:8765/api/v1/memories/?user_id=visionary-architects`

- **Delete memory**: `DELETE http://localhost:8765/api/v1/memories/{memory_id}`

If the API is unreachable, tell BALLER: "OpenMemory isn't responding at localhost:8765. Start it with `docker compose -f D:\DEV_PROJECTS\mem0\openmemory\docker-compose.yml up -d`"

## Session Start Protocol (PROACTIVE)

When starting a new session with BALLER, before doing anything else:

1. **Query recent memories** — search for the last 24 hours of activity
2. **Surface relevant context** — if there are recent memories from OTHER tools, proactively tell BALLER what happened
3. **Check for unresolved items** — look for memories tagged with open questions or blockers

The goal: BALLER never has to say "let me catch you up." You already know.

## Memory Write Protocol (MANDATORY)

### Always Write
- **Decisions**: Any choice between alternatives. Tag: `type: "decision"`
- **Preferences**: How BALLER wants things done. Tag: `type: "preference"`
- **State changes**: System config changes. Tag: `type: "state-change"`
- **Plans created/updated**: Tag: `type: "plan"`
- **Fixes applied**: Bugs diagnosed and fixed. Tag: `type: "fix"`

### Never Write
- Routine chat without decisions or context
- Info already in the GitHub knowledge hub truth-pack
- Temporary debugging output
- Secrets, API keys, passwords

### Quality Rules
- Lead with the conclusion/decision, not the backstory
- Include the "why" — future tools need the rationale
- Use absolute dates (2026-03-20), never relative
- One memory per topic — don't combine unrelated items
- Tag the source tool accurately

## Session End Protocol (MANDATORY)

Before ending any session where meaningful work happened:

1. **Write a session summary memory** with source, type "context", topic "session-handoff"
2. **Write to the GitHub knowledge hub** — append to SESSION_LOG.md, DECISIONS.md, ACTIVE_PLANS.md as needed
3. **Commit and push** the knowledge hub changes

## Context Recovery Commands

| BALLER says | What to do |
|------------|-----------|
| "What was I working on?" | Search recent memories, group by topic, present chronologically |
| "What did we decide about X?" | Search for decisions related to X, show rationale |
| "Catch me up" | Pull last 24-48 hours from ALL tools, summarize |
| "What's the status of X?" | Search plan memories for X, show current phase |
| "Remember this: [thing]" | Write immediately with appropriate tags |
| "Forget this: [thing]" | Search and delete the memory |

## ADHD-Specific Patterns

1. **Don't ask permission to write memories.** Just write them. Asking interrupts flow.
2. **Surface context proactively.** If you detect a context switch, say "Last time you worked on this, you were at [point X]."
3. **Keep summaries scannable.** Short bullets, not paragraphs.
4. **Track the meta-level.** If BALLER keeps re-explaining something, write a memory about that pattern.
5. **Never say "I don't have context."** Say "Let me check shared memory" and search first.

## Source Tool Names (Use Exactly)

| Tool | Source tag |
|------|-----------|
| Claude Code CLI | `claude-code` |
| Codex CLI | `codex-cli` |
| Gemini CLI | `gemini-cli` |
| Copilot CLI | `copilot-cli` |
| Claude Desktop | `claude-desktop` |
| ChatGPT Desktop | `chatgpt-desktop` |
| BabyClaw | `babyclaw` |
| Perplexity Pro | `perplexity` |
| Comet Browser | `comet` |

## Memory Type Tags

| Tag | When |
|-----|------|
| `decision` | Choice between alternatives |
| `preference` | How BALLER wants something done |
| `context` | Background info, session summaries |
| `plan` | Plan created, updated, or completed |
| `fix` | Bug found and resolved |
| `state-change` | System configuration changed |

## Troubleshooting

| Issue | Fix |
|-------|-----|
| API timeout on first write | Normal cold start. Retry after 30s. |
| Connection refused | Start OpenMemory: `docker compose -f D:\DEV_PROJECTS\mem0\openmemory\docker-compose.yml up -d` |
| Memories not found | Check user_id is `visionary-architects`. Try broader search. |
