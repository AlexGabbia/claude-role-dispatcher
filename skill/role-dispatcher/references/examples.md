# Dispatching Examples

## Example 1: SKIP (No Dispatch)

**User request**: "What does useEffect do in React?"

### Triage Decision
- **Lane**: SKIP
- **Reason**: Simple factual question, no implementation, no code involved
- **Action**: Answer directly as generalist. No agents dispatched.

-> Dispatcher answers the question directly without any role matching or agent dispatch.

---

## Example 2: FAST (Single Agent, No Confirmation)

**User request**: "Fix this TypeScript error: Property 'name' does not exist on type '{}'."

### Triage Decision
- **Lane**: FAST
- **Reason**: Single domain (Software Development), 1 obvious role (Frontend/Full-Stack Developer), low complexity

### Dispatch Decision
- **Category match**: Software Development (keyword: TypeScript)
- **Role**: Frontend Developer
- **Agents**: 1
- **Model**: Sonnet (debugging, focused scope)

-> Dispatcher skips confirmation (Step 6) and immediately dispatches a Frontend Developer agent on Sonnet.

---

## Example 3: Simple with Confirmation (Single Agent, FULL)

**User request**: "How do I optimize this SQL query that's taking 30 seconds?"

### Triage Decision
- **Lane**: FULL (could be FAST, but query optimization may involve architecture discussion — defaults to FULL for ambiguous scope)

### Dispatch Decision
- **Category match**: Infrastructure & Ops (keyword: database)
- **Role**: Database Administrator (DBA)
- **Agents**: 1
- **Review Agent**: No
- **Model**: Sonnet (debugging, focused scope)

### Propose & Confirm

```
**Proposed dispatch:**
- Database Administrator (DBA): SQL query optimization and execution plan analysis

**Model:** claude-sonnet-4-6 (medium complexity)

Proceed? You can:
- **Enter/yes** to proceed as proposed
- **Change roles**: e.g. "use Data Engineer instead"
- **Change model**: e.g. "use opus" or "use haiku"
- **Both**: e.g. "use Data Engineer with haiku"
```

**User**: "yes"
-> Dispatcher proceeds with DBA agent on Sonnet.

---

## Example 4: Medium (Two Agents)

**User request**: "I need to build a REST API with authentication and a React dashboard to manage users"

### Dispatch Decision
- **Categories**: Software Development + Cybersecurity
- **Roles**: Backend Developer + Frontend Developer
- **Independence**: MIXED (API design independent of UI, but UI depends on API endpoints)
- **Agents**: 2 (parallel for design, sequential for integration)
- **Review Agent**: Yes
- **Model**: Sonnet (multi-file feature, but well-defined scope)

### Propose & Confirm

```
**Proposed dispatch:**
- Backend Developer: REST API design with authentication
- Frontend Developer: React dashboard for user management

**Model:** claude-sonnet-4-6 (medium complexity)

Proceed? You can:
- **Enter/yes** to proceed as proposed
- **Change roles**: e.g. "add Security Engineer"
- **Change model**: e.g. "use opus"
- **Both**: e.g. "add Security Engineer with opus"
```

**User**: "yes"
-> Dispatcher proceeds with 2 agents on Sonnet.

### Collaboration
- Backend agent provides: endpoint list, auth flow, response schemas
- Frontend agent receives handoff notes with API contract
- Review agent checks: API contract matches frontend expectations, auth flow is consistent

---

## Example 5: Complex (Three Agents)

**User request**: "Design a microservices architecture for our e-commerce platform with payment processing, inventory management, and real-time notifications"

### Dispatch Decision
- **Categories**: Consulting & Training + Software Development + Infrastructure & Ops
- **Roles**: Software Architect + Backend Developer + DevOps Engineer
- **Independence**: DEPENDENT (architecture first, then implementation, then deployment)
- **Agents**: 3 (sequential)
- **Review Agent**: Yes (mandatory for 3-agent coordination)
- **Model**: Opus (architecture + multi-agent coordination)

### Propose & Confirm

```
**Proposed dispatch:**
- Software Architect: microservices boundaries and communication patterns
- Backend Developer: service implementation and API contracts
- DevOps Engineer: deployment strategy and CI/CD pipeline

**Model:** claude-opus-4-6 (high complexity)

Proceed? You can:
- **Enter/yes** to proceed as proposed
- **Change roles**: e.g. "use Security Architect instead of Backend Developer"
- **Change model**: e.g. "use sonnet"
- **Both**: e.g. "remove DevOps Engineer, use sonnet"
```

**User**: "yes"
-> Dispatcher proceeds with 3 agents on Opus.

### Execution Sequence
1. **Software Architect** (first): Defines microservice boundaries, communication patterns, data ownership
2. **Backend Developer** (second): Receives architecture, designs service implementations, API contracts, data models
3. **DevOps Engineer** (third): Receives architecture + implementation plan, designs deployment, CI/CD, monitoring

### Review Agent Assessment
- Checks architecture is implementable with proposed tech stack
- Verifies deployment strategy supports the architectural patterns
- Validates all three outputs form a coherent, buildable system
- Aggregate confidence: minimum of three agents

---

## Example 6: User Override (Role + Model Change)

**User request**: "Set up a CI/CD pipeline for our Node.js monorepo with staging and production environments"

### Dispatch Decision
- **Category match**: Infrastructure & Ops
- **Roles**: DevOps Engineer + SRE
- **Agents**: 2
- **Review Agent**: Yes
- **Model**: Sonnet

### Propose & Confirm

```
**Proposed dispatch:**
- DevOps Engineer: CI/CD pipeline design and implementation
- Site Reliability Engineer (SRE): staging/production environment setup and monitoring

**Model:** claude-sonnet-4-6 (medium complexity)

Proceed? You can:
- **Enter/yes** to proceed as proposed
- **Change roles**: e.g. "use Cloud Engineer instead of SRE"
- **Change model**: e.g. "use opus" or "use haiku"
- **Both**: e.g. "use only DevOps Engineer with haiku"
```

**User**: "use only DevOps Engineer with haiku"

-> Dispatcher adjusts: 1 agent (DevOps Engineer), model changed to Haiku, no review agent needed.

---

## Example 7: Large-Scale Project (Agent Team)

**User request**: "Build a complete SaaS project management tool with user auth, team workspaces, real-time collaboration, Stripe billing, email notifications, and an admin dashboard"

### Dispatch Decision
- **Categories**: Software Development + Design & UX + Infrastructure & Ops + Cybersecurity + E-commerce
- **Roles**: Software Architect, Backend Developer, Frontend Developer, UX/UI Designer, DevOps Engineer, Security Engineer, Payment Integration Specialist
- **Independence**: Highly interdependent — architecture drives all layers, security cross-cuts everything
- **Agents**: 7 (Agent Team mode — too many agents for subagent coordination, deep collaboration needed)
- **Model**: Opus (large-scale architecture + multi-agent coordination)

### Propose & Confirm

```
**Proposed dispatch:**
- Software Architect: system architecture, service boundaries, data models
  Key skills: system design, microservices, event-driven architecture
- Backend Developer: API design, auth, real-time WebSocket layer
  Key skills: Node.js, Python, REST, GraphQL, WebSockets
- Frontend Developer: React app, real-time UI, responsive design
  Key skills: React, TypeScript, state management, WebSocket clients
- UX/UI Designer: user flows, wireframes, design system
  Key skills: Figma, design systems, user research, accessibility
- DevOps Engineer: CI/CD, cloud infrastructure, monitoring
  Key skills: Docker, Kubernetes, Terraform, GitHub Actions
- Security Engineer: auth architecture, data protection, compliance
  Key skills: OAuth, RBAC, encryption, OWASP, GDPR
- Payment Integration Specialist: Stripe billing, subscription management
  Key skills: Stripe API, recurring billing, webhook handling

**Model:** claude-opus-4-6 (high complexity)
**Execution:** MIXED (architecture first, then parallel implementation)
**Dispatch mode:** Agent Team — 7 agents with cross-cutting dependencies need shared coordination

Proceed? You can:
- **yes** to proceed as proposed
- **Change roles**: e.g. "remove UX/UI Designer"
- **Change model**: e.g. "use sonnet"
- **use subagents**: force subagent mode instead of team
- **Combine**: e.g. "remove Payment Specialist, use sonnet"
```

**User**: "yes"
-> Dispatcher creates team via TeamCreate, spawns Software Architect as Team Lead, then 6 teammates.

### Team Execution
1. **Team Lead (Software Architect)** creates tasks: "Define service boundaries", "Design data models", "API contract spec", "Auth architecture", "Deployment strategy", "UI component inventory", "Billing integration spec"
2. Teammates claim tasks matching their expertise
3. Security Engineer broadcasts: "All services must use JWT with refresh tokens, RBAC on every endpoint"
4. Frontend Developer messages Backend Developer: "Need WebSocket event schema for real-time updates"
5. Team Lead monitors progress, resolves conflicts (e.g., REST vs GraphQL debate)
6. Team Lead synthesizes all deliverables into a unified architecture document

---

## Example 8: Competing Hypotheses / Debugging (Agent Team)

**User request**: "Our API response times jumped from 50ms to 2s after yesterday's deploy. We changed the ORM, added a new caching layer, and updated the load balancer config. Need to find the root cause."

### Dispatch Decision
- **Categories**: Infrastructure & Ops + Software Development + QA & Testing
- **Roles**: Database Administrator, Backend Developer, Performance Tester, SRE, DevOps Engineer
- **Independence**: INDEPENDENT investigation, but agents need to **challenge each other's hypotheses**
- **Agents**: 5 (Agent Team mode — competing hypotheses require discussion between agents)
- **Model**: Opus (complex debugging + multi-agent debate)

### Propose & Confirm

```
**Proposed dispatch:**
- Database Administrator: ORM migration analysis, query plan regression
  Key skills: query optimization, ORM tuning, execution plans
- Backend Developer: ORM code review, N+1 queries, connection pooling
  Key skills: ORM patterns, connection management, profiling
- Performance Tester: load testing, response time profiling, bottleneck isolation
  Key skills: JMeter, profiling, latency analysis, benchmarking
- Site Reliability Engineer: load balancer config review, traffic patterns
  Key skills: load balancing, traffic analysis, monitoring, alerting
- DevOps Engineer: deployment diff analysis, infrastructure changes
  Key skills: deploy pipelines, config management, rollback procedures

**Model:** claude-opus-4-6 (high complexity)
**Execution:** INDEPENDENT (parallel investigation)
**Dispatch mode:** Agent Team — competing hypotheses need cross-agent discussion

Proceed? You can:
- **yes** to proceed as proposed
- **Change roles**: e.g. "add Security Engineer"
- **Change model**: e.g. "use sonnet"
- **use subagents**: force subagent mode instead of team
```

**User**: "yes"
-> Dispatcher creates team, spawns DBA as Team Lead (database is primary suspect), then 4 teammates.

### Team Execution
1. Each agent investigates their hypothesis in parallel
2. DBA broadcasts: "ORM is generating 3 extra JOINs per query — likely culprit"
3. Backend Developer responds: "Confirmed N+1 pattern in the new ORM mapping, but connection pool is also misconfigured"
4. SRE messages: "Load balancer change is clean — no impact on latency"
5. Performance Tester: "Profiling confirms 80% of latency is in DB layer, 20% in connection pool exhaustion"
6. Team Lead synthesizes: root cause is ORM migration (N+1 + extra JOINs) compounded by connection pool misconfiguration. Proposes fix with rollback strategy.
