---
name: role-dispatcher
description: >
  Analyzes user requests and dispatches specialized AI agents from 209+ IT professional roles across 14 categories.
  Use this skill for ANY technical or IT-related task: building features, fixing bugs, designing systems,
  reviewing code, setting up infrastructure, planning architecture, security audits, data pipelines, game development,
  marketing strategy, and more. Even simple requests like "fix this bug" or "create a landing page" benefit from
  specialist matching. Triggers on: build, create, develop, design, deploy, debug, fix, optimize, architect, secure,
  test, analyze, migrate, automate, review, audit, plan, manage, refactor, scale, monitor, integrate, configure, implement.
---

# Role Dispatcher v2

## Purpose

Match user requests to the most relevant IT specialist roles (209+ across 14 categories), dispatch specialized agents, coordinate their collaboration, and synthesize results.

## Step 1: Language Detection & Gate Check

**Language**: Detect the user's language. Set `RESPONSE_LANGUAGE` for all outputs. Internal matching always uses English keywords regardless of input language.

**Gate check** — Skip dispatch if ALL of these are true:
- The request is a simple factual question with no implementation component
- No code, architecture, or technical workflow is involved
- A general-purpose response would fully satisfy the request

If in doubt, proceed with dispatch — a specialist perspective adds value even for seemingly simple questions. "How do I center a div?" is better answered by a Frontend Developer than a generalist.

## Step 2: Analyze Request & Match Categories

Read `references/role-index.md` to identify 1-3 matching categories using this two-pass approach:

**Pass 1 — Keyword scan**: Match the request against category keywords. Most requests match clearly here.

**Pass 2 — Semantic inference** (if Pass 1 is ambiguous or yields 0 matches): Consider the intent behind the request, not just literal words. Example: "make my app faster" has no direct keyword, but maps to Infrastructure & Ops (performance) or QA & Testing (performance testing) depending on context.

**Edge cases:**
- **No match found**: Tell the user no specialist role fits well, then answer directly as a generalist
- **Too many matches (4+)**: Narrow down by asking the user which aspect they want to focus on first
- **Ambiguous match**: Prefer the category that covers the primary deliverable, not the secondary concern

Read ONLY the matched role files from `assets/roles/`.

## Step 3: Select Specific Roles & Agent Count

Within the matched category files, select the best role(s) by comparing the request against each role's **Description** and **Key Skills** columns.

| Complexity | Agents | Review Agent | Signal |
|------------|--------|-------------|--------|
| Single domain, clear deliverable | 1 | No | "Write a React component", "Optimize this query" |
| Two domains with integration points | 2 | Yes | "Build API + frontend", "Design and deploy" |
| Multi-domain, architectural scope | 3 | Yes | "Design a secure e-commerce platform" |

**Max 3 agents.** If more domains are involved, group related competencies into a composite role (e.g., "Full-Stack Developer" covers both frontend and backend rather than dispatching two separate agents).

**Role selection heuristic**: Pick the most specialized role that covers the request. "Set up Kubernetes cluster" should match Kubernetes Administrator (specific) over Cloud Engineer (broad). Use the Key Skills column to break ties between similar roles.

## Step 4: Classify Dependencies

Read `references/collaboration-protocol.md` for the full protocol. Quick reference:

- **INDEPENDENT**: No data flow between agents -> run in **parallel**
- **DEPENDENT**: Output of one feeds another -> run **sequentially**, pass handoff notes
- **MIXED**: Parallel first, then sequential for dependent parts

## Step 5: Select Model

Read `references/model-selection-guide.md` for the full matrix. Quick reference:

| Complexity | Model |
|------------|-------|
| Q&A, small edits | `claude-haiku-4-5-20251001` |
| Features, debugging, single-domain | `claude-sonnet-4-6` |
| Architecture, security, multi-agent | `claude-opus-4-6` |

## Step 6: Propose & Confirm

Present the dispatch plan and **wait for user confirmation** before proceeding. This step is mandatory — never skip it.

```
**Proposed dispatch:**
- {Role Name}: {brief contribution description}
  Key skills: {key skills from role file}
- {Role Name}: {brief contribution description}
  Key skills: {key skills from role file}

**Model:** {model} ({complexity} complexity)
**Execution:** {INDEPENDENT | DEPENDENT | MIXED}

Proceed? You can:
- **yes** to proceed as proposed
- **Change roles**: e.g. "use Security Engineer instead of Backend Developer"
- **Change model**: e.g. "use opus" or "use haiku"
- **Both**: e.g. "use only Frontend Developer with sonnet"
```

If the user modifies roles or model, adjust and proceed. If they confirm, proceed with the original proposal.

## Step 7: Dispatch Agents

Read `references/prompt-templates.md` for the full template. For each role, create an agent using the `Agent` tool with `subagent_type: "general-purpose"`.

Each agent prompt MUST include:
1. **Role identity**: Name, description, and key skills from the role file
2. **Expertise framing**: "You approach this as a {Role Name} with deep expertise in {Key Skills}"
3. **The concrete task** from the user's request
4. **Language directive**: Respond in `{RESPONSE_LANGUAGE}`
5. **Confidence requirement**: Rate HIGH / MEDIUM / LOW with brief justification
6. **Collaboration context** (if multi-agent): Focus area, what other agents cover, handoff notes format

**Parallel dispatch**: Launch all INDEPENDENT agents in a single message with multiple Agent tool calls. For DEPENDENT agents, wait for each to complete before dispatching the next with handoff notes.

## Step 8: Review & Synthesize

**Single agent**: Present the result directly. If confidence is LOW, flag it to the user.

**Multi-agent**: Apply the Review Agent protocol from `references/collaboration-protocol.md`:
1. Check completeness against the original request
2. Detect conflicts between agent outputs
3. Verify integration (pieces fit together)
4. Compute aggregate confidence = minimum of individual confidences
5. Decision: APPROVE / NEEDS_REVISION / ESCALATE

If **ESCALATE**: Present conflicting perspectives with pros/cons. Let the user decide.
If **APPROVE**: Synthesize into a single coherent response in `{RESPONSE_LANGUAGE}`.

## Rules

- Respond in the user's detected language
- Max 3 agents per request
- All file paths are relative to this skill directory (use `assets/roles/` and `references/`)
- For detailed examples, read `references/examples.md`
- Use `Agent` tool with `subagent_type: "general-purpose"` for each agent
- Agents run in parallel when INDEPENDENT, sequentially when DEPENDENT
- LOW confidence from any agent triggers automatic ESCALATE
- Always wait for user confirmation in Step 6 before dispatching agents
- Prefer the most specialized role over a generalist one when both could apply
