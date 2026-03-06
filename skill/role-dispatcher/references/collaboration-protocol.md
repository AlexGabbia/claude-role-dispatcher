# Agent Collaboration Protocol

## 1. Independence Check

Before dispatching agents, classify the task:

| Type | Definition | Execution | Example |
|------|-----------|-----------|---------|
| **INDEPENDENT** | Agents work on separate concerns with no data dependencies | Parallel | "Build API + design UI" |
| **DEPENDENT** | One agent's output feeds into another's input | Sequential | "Design schema, then build API on it" |
| **MIXED** | Some parts independent, some dependent | Parallel first, then sequential | "Research + design (parallel), then implement (sequential)" |

## 2. Dispatch Mode Selection

Choose the dispatch mechanism based on agent count and collaboration needs:

| Agents | Collaboration needed? | Mode | Why |
|--------|----------------------|------|-----|
| 1 | N/A | **Single subagent** | Simple, focused task |
| 2-4, independent | No | **Parallel subagents** | Fast, low overhead, results return to dispatcher |
| 2-4, dependent | Minimal (handoff notes) | **Sequential subagents** | One feeds the next, dispatcher coordinates |
| 5+ OR agents need to discuss/challenge each other | Yes | **Agent Team** (TeamCreate) | Shared tasks, direct messaging, self-coordination |

**User overrides always take priority:**
- "use a team" → Agent Team mode, even for 2 agents
- "use subagents" → Subagent mode, even for 5+ agents

### When to prefer Agent Teams even with fewer agents

- Agents need to **debate or challenge** each other's assumptions (e.g., competing architecture proposals)
- The task requires **iterative refinement** where agents build on each other's work in multiple rounds
- Cross-cutting concerns where every agent needs visibility into every other agent's output

## 3. Agent Team Coordination

### Team Creation

1. Use `TeamCreate` with a descriptive name (e.g., "SaaS Platform Team") and the overall project goal
2. Spawn the **Team Lead** first — this agent orchestrates the others
3. Spawn remaining **teammates** with their role-specific prompts

### Team Lead Responsibilities

- Creates tasks in the shared task list for each work area
- Assigns or lets teammates claim tasks based on their expertise
- Monitors progress and sends messages to unblock teammates
- Resolves conflicts between teammates
- Acts as the **Review Agent** — synthesizes the final deliverable
- Delivers the unified result back to the dispatcher

### Teammate Coordination

- **Claim tasks** from the shared task list that match their expertise
- **Send messages** to other teammates when they need input or have dependencies
- **Broadcast** important decisions that affect multiple teammates
- **Mark tasks complete** with their deliverables attached
- Stay within their role's domain — flag cross-cutting concerns to the Team Lead

### When to Message vs. Broadcast

| Action | Use |
|--------|-----|
| **Direct message** | One teammate needs specific info from another |
| **Broadcast** | A decision affects the whole team (e.g., "we're using PostgreSQL, not MongoDB") |
| **Task update** | Delivering a completed work item |

## 4. Review Agent Protocol

**When required**: Always when 2+ agents are dispatched (subagent mode). In Agent Team mode, the Team Lead fulfills this role.

The Review Agent activates AFTER all specialist agents complete. Its responsibilities:

1. **Validate completeness**: Every part of the user's request is addressed
2. **Detect conflicts**: Contradicting recommendations between agents
3. **Verify integration**: Outputs fit together into a coherent whole
4. **Assess aggregate confidence**: Overall confidence = minimum of all individual agent confidences

### Review Agent Decisions

| Decision | When | Action |
|----------|------|--------|
| **APPROVE** | No conflicts, complete coverage, HIGH/MEDIUM confidence | Synthesize and present to user |
| **NEEDS_REVISION** | Minor gaps or inconsistencies | Request specific agent to revise |
| **ESCALATE** | Critical conflicts or LOW confidence | Present both perspectives with pros/cons, let user decide |

## 5. Conflict Resolution

When agents disagree:

1. **Identify the conflict**: What exactly do they disagree on?
2. **Present both sides**: Each agent's position with reasoning
3. **Provide trade-offs**: Pros and cons of each approach
4. **Do NOT auto-resolve**: Let the user decide. Present options clearly:

```
The agents provided different recommendations on [topic]:

**Option A** ({ROLE_1}): {recommendation}
- Pros: ...
- Cons: ...

**Option B** ({ROLE_2}): {recommendation}
- Pros: ...
- Cons: ...

Which approach do you prefer?
```

## 6. Confidence Scoring

### Individual Agent Confidence

| Level | Meaning | Criteria |
|-------|---------|----------|
| **HIGH** | Strong match | Role's core expertise directly applies to the task |
| **MEDIUM** | Adjacent match | Role has relevant knowledge but task is at the edge of expertise |
| **LOW** | Partial match | Role can contribute but another specialist would be better |

### Aggregate Confidence

- **Aggregate = minimum of all individual confidences**
- If ANY agent reports LOW -> escalate to user with transparency
- If all HIGH -> high confidence in the synthesized answer
- Mixed HIGH/MEDIUM -> proceed with medium confidence, note limitations

## 7. Handoff Protocol (Subagent Mode)

When agents need to pass information:

1. **Source agent** includes in Handoff Notes:
   - What data/decisions the next agent needs
   - Constraints or assumptions made
   - Open questions that affect downstream work

2. **Receiving agent** gets the handoff notes prepended to their prompt:
   ```
   ### Context from {SOURCE_ROLE}
   {HANDOFF_NOTES}

   Consider these inputs when working on your task.
   ```

## 8. Escalation Triggers

Automatically escalate to user when:
- Any agent reports LOW confidence
- Agents produce contradicting recommendations
- The task requires domain knowledge not covered by available roles
- The estimated complexity exceeds what agents can handle autonomously
