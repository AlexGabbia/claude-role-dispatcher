# Context Persistence Protocol

## Purpose

Prevent context rot in long-running tasks by giving agents persistent work logs and a mechanism for context restart. When an agent approaches context saturation, it checkpoints its progress and a fresh agent continues from the saved state.

## When to Enable (PERSIST=YES)

Enable context persistence when ALL of these apply:
- Task is on the **FULL** path (not SKIP or FAST)
- Medium-high complexity: 3+ agents OR estimated 30+ tool calls per agent
- Task involves multi-phase work where losing context would cause rework

**Skip persistence** (PERSIST=NO) when:
- SKIP or FAST lane tasks
- Estimated < 20 tool calls total
- Single agent on a straightforward task

## Directory Structure

Context persistence uses a `.dispatch/` directory in the project root:

```
.dispatch/
  STATE.md              # Global dispatch state (created by dispatcher)
  {agent-id}-log.md     # Work log per agent (e.g., backend-01-log.md)
```

## STATE.md Format (< 50 lines)

Created by the dispatcher before spawning agents. Updated by the dispatcher or Team Lead as agents complete work.

```markdown
# Dispatch State

## Project Goal
{One-line description of the user's request}

## Agent Roster

| ID | Role | Focus Area | Status |
|----|------|------------|--------|
| backend-01 | Backend Developer | REST API + auth | IN_PROGRESS |
| frontend-01 | Frontend Developer | React dashboard | IN_PROGRESS |
| security-01 | Security Engineer | Auth architecture | COMPLETE |

## Global Decisions
- {Decision 1}: {rationale}
- {Decision 2}: {rationale}

## Status Summary
- backend-01: Completed schema design, working on endpoints (3/7)
- frontend-01: Component library set up, building auth pages
- security-01: Delivered auth spec, JWT + RBAC approach
```

## Agent Work Log Format (< 80 lines)

Each agent maintains its own log at `.dispatch/{AGENT_ID}-log.md`. The log is the agent's persistent memory across context restarts.

```markdown
---
agent: Backend Developer
id: backend-01
phase: 2 of 4
tool_calls: ~37
status: IN_PROGRESS
last_updated: 2026-03-06T14:30:00
---

## Completed
- [x] Designed database schema (PostgreSQL, 8 tables)
- [x] Implemented user authentication (JWT + refresh tokens)
- [x] Set up project structure and dependencies

## In Progress
- [ ] REST API endpoints (3 of 7 done: users, auth, teams)

## Key Decisions
- PostgreSQL over MongoDB (relational data model fits better)
- JWT with 15min access + 7d refresh tokens
- Express.js with Zod validation

## Next Steps
- Complete remaining 4 endpoints (projects, tasks, billing, notifications)
- Add validation middleware for all endpoints
- Write integration tests for auth flow

## Files Modified
- src/db/schema.sql
- src/auth/jwt.service.ts
- src/routes/users.ts
- src/routes/auth.ts
- src/routes/teams.ts
```

### Log Rules

- **Max 80 lines**. If the log grows beyond this, summarize older completed items into a single line (e.g., "Phase 1: schema + auth + project setup [DONE]")
- **YAML frontmatter** is mandatory: agent, id, phase, tool_calls (approximate), status, last_updated
- **Status values**: `IN_PROGRESS`, `CHECKPOINT`, `COMPLETE`
- Update the log at every checkpoint, not continuously

## Checkpoint Triggers

Agents self-monitor and checkpoint based on these heuristics (in priority order):

### 1. Phase Boundary (highest priority)
After completing a logical phase of work (e.g., finishing all database work before moving to API layer). **Always checkpoint at phase boundaries** even if tool call count is low.

### 2. Tool Call Count
- **~25 tool calls**: Write/update the work log (soft checkpoint)
- **~40 tool calls**: If significant work remains, write the log and signal for continuation (hard checkpoint)

### 3. File Count
If the agent has read or modified **15+ distinct files**, checkpoint regardless of tool call count.

### 4. Self-Monitoring
Checkpoint immediately if the agent notices:
- Re-reading files it already processed earlier in the session
- Losing precision in its responses or forgetting earlier decisions
- Repeating analysis it already performed

### Phase-First Rule
If the agent is close to finishing a phase (< 5 tool calls away), **finish the phase first** before checkpointing. A clean phase boundary makes continuation much smoother.

## Checkpoint Signal

When an agent needs continuation (hard checkpoint):

**Subagent mode**: End the output with:
```
<!-- CHECKPOINT:CONTINUE -->
Checkpoint: Work log saved to `.dispatch/{AGENT_ID}-log.md`. Remaining work: {brief summary}.
```

**Agent Team mode**: Send a message to the Team Lead:
```
CHECKPOINT: Work log saved to `.dispatch/{AGENT_ID}-log.md`. I need a fresh continuation to complete: {remaining work summary}.
```

## Continuation Protocol

When a checkpoint signal is received:

### Subagent Mode
1. Dispatcher reads `.dispatch/{AGENT_ID}-log.md`
2. Dispatcher spawns a **new** Agent tool call with the same role, using the **Continuation Agent Template** from `references/prompt-templates.md`
3. The fresh agent reads the log and resumes from "Next Steps"
4. The continuation agent follows the same checkpoint protocol — it may checkpoint again if needed
5. Repeat until the agent completes without a CHECKPOINT signal

### Agent Team Mode
1. Team Lead reads the teammate's log file
2. Team Lead spawns a **new** teammate with the same role and a reference to the log file
3. The fresh teammate reads the log, resumes work, and reports back to the Team Lead
4. Team Lead updates `.dispatch/STATE.md` to reflect the continuation

## Relationship with Other Protocols

| Protocol | Purpose | Scope |
|----------|---------|-------|
| **Handoff notes** (collaboration-protocol.md Section 7) | Pass info between DIFFERENT agents | Cross-agent |
| **Work logs** (this protocol) | Pass info between INSTANCES of the SAME agent | Same-agent continuity |
| **STATE.md** | Global view for dispatcher/Team Lead | Cross-agent overview |

All three coexist. Key decisions recorded in a work log also serve as context for other agents reading STATE.md.

## Cleanup

After the task completes and the dispatcher synthesizes the final result:
- Inform the user that dispatch state is saved in `.dispatch/`
- Suggest adding `.dispatch/` to `.gitignore` if desired
- The `.dispatch/` directory can be deleted after the task is fully complete, or kept for reference
