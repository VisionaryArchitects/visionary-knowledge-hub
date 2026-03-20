# SESSION HANDOFF PROTOCOL — MANDATORY FOR ALL AI TOOLS

## THIS IS NON-NEGOTIABLE. EVERY AI MUST FOLLOW THIS.

At the END of every session where ANY of the following occurred:
- A decision was made
- A plan was created or modified
- System state changed (ports, configs, services, versions)
- A significant piece of work was completed
- A problem was diagnosed and fixed

You MUST append a handoff block to the appropriate file in `visionary-knowledge-hub`.

## WHERE TO WRITE (HARD-CODED — NO EXCEPTIONS)

| What happened | File to append to | Format |
|--------------|-------------------|--------|
| Decision made | `DECISIONS.md` | Decision block (see below) |
| Plan created/updated | `ACTIVE_PLANS.md` | Plan block (see below) |
| Session ending | `SESSION_LOG.md` | Session handoff block (see below) |
| System state changed | Update the relevant `truth-pack/*.md` file | Inline edit |

## SESSION HANDOFF BLOCK FORMAT

```markdown
---
## [YYYY-MM-DD HH:MM] — [Tool Name]
**Summary:** [1-2 sentences of what happened]
**Decisions:**
- [decision 1]
- [decision 2]
**State Changes:**
- [what changed]
**Next Steps:**
- [what should happen next]
**Open Questions:**
- [anything unresolved]
---
```

## DECISION BLOCK FORMAT

```markdown
---
## [YYYY-MM-DD] — [Short Decision Title]
**Tool:** [which AI made/recorded this]
**Context:** [why this decision was needed]
**Decision:** [what was decided]
**Rationale:** [why this option was chosen]
**Alternatives Considered:** [what was rejected and why]
**Impact:** [what this changes in the system]
---
```

## PLAN BLOCK FORMAT

```markdown
---
## [Plan Name] — [Status: ACTIVE/COMPLETED/BLOCKED/ABANDONED]
**Created:** [date] by [tool]
**Last Updated:** [date] by [tool]
**Owner:** BALLER
**Summary:** [what this plan accomplishes]
**Phases:**
1. [phase 1] — [status]
2. [phase 2] — [status]
3. [phase 3] — [status]
**Blockers:** [if any]
**Related Docs:** [links to truth-pack docs or other plans]
---
```

## ENFORCEMENT RULES

1. NEVER skip the handoff. If you made decisions or changed things, LOG IT.
2. NEVER put handoff content anywhere else. These three files are the ONLY targets.
3. ALWAYS append — never overwrite or reorganize existing entries.
4. ALWAYS include the tool name so we know which AI wrote it.
5. ALWAYS use absolute dates (2026-03-20), never relative ("yesterday", "last week").
6. If you can't write to the repo directly, output the handoff block and tell BALLER to paste it.
