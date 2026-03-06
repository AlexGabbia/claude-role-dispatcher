# Model Selection Guide

## Decision Matrix

| Task Type | Agents | Review Agent | Model | Rationale |
|-----------|--------|-------------|-------|-----------|
| Q&A / Explanation | 1 | No | `claude-haiku-4-5-20251001` | Simple knowledge retrieval, fast response |
| Single-file code generation | 1 | No | `claude-sonnet-4-6` | Good code quality, balanced speed |
| Code review / debugging | 1-2 | Optional | `claude-sonnet-4-6` | Needs reasoning but focused scope |
| Multi-file feature | 2 | Yes | `claude-sonnet-4-6` | Multiple concerns, needs coordination |
| Architecture / system design | 2-3 | Yes | `claude-opus-4-6` | Deep reasoning, complex trade-offs |
| Security audit / review | 1-2 | Yes | `claude-opus-4-6` | Critical accuracy, no room for error |
| Multi-agent coordination (subagents) | 3-4 | Yes | `claude-opus-4-6` | Complex orchestration, synthesis |
| Agent Team coordination | 5+ | Yes (Team Lead) | `claude-opus-4-6` | Large-scale orchestration, team self-coordination |

## Quick Decision Flow

```
Is it a simple question or small edit?
  YES -> Haiku
  NO  -> Does it require multi-file changes or 2+ agents?
    NO  -> Sonnet
    YES -> Is it architecture, security, 3+ agent coordination, or Agent Team?
      NO  -> Sonnet
      YES -> Opus
```

## Cost-Performance Trade-offs

| Model | Speed | Quality | Cost | Best For |
|-------|-------|---------|------|----------|
| Haiku | Fast | Good | Low | Quick answers, simple tasks, explanations |
| Sonnet | Medium | Very Good | Medium | Most development work, debugging, features |
| Opus | Slower | Excellent | High | Complex architecture, security, multi-agent synthesis |

## Override Rules

- **Always use Opus** for: security-sensitive tasks, system architecture, tasks touching production infrastructure, Agent Team coordination
- **Never use Haiku** for: multi-file code changes, security analysis, architecture decisions, Agent Team mode
- **Agent Teams note**: The Team Lead should always use Opus. Individual teammates can use Sonnet for focused implementation tasks if the user requests a lighter model — the Team Lead still coordinates on Opus
- **User override**: If the user explicitly requests a model, respect their choice regardless of this guide

## User Override Protocol

During Step 6 (Propose & Confirm), the user can override the suggested model before agents are dispatched.

**How it works:**
1. The dispatcher proposes roles and a model based on the decision matrix above
2. The user can accept or change the model in their confirmation response
3. Valid override formats:
   - `"use opus"` / `"use sonnet"` / `"use haiku"`
   - `"use claude-opus-4-6"` (full model ID)
   - Combined with role changes: `"use only Frontend Developer with haiku"`
4. The dispatcher applies the override and proceeds with the user's chosen model

**Important:** A user override always takes priority over the decision matrix. If the user picks a lighter model for a complex task, respect their choice — they may be optimizing for speed or cost.
