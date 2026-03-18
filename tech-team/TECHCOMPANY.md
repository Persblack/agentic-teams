# AI Agent Company — Full Product Organization Blueprint

> **Purpose:** Map out how an AI agentic swarm could replicate a full tech company — from brainstorming through release, marketing, and maintenance. This is an architectural overview, not individual role specifications.

---

## 1. Design Principles

### Why replicate real company structure?

Real companies evolved their roles and processes for good reasons: coordination at scale, specialization, accountability, and quality gates. An AI agent system faces the same challenges — just with tokens instead of salaries. If sprints, standups, and retrospectives solve coordination problems for humans, the AI equivalent solves them for agents.

### The subrole question: when to split?

**More roles = more coordination overhead.** Every role boundary is a handoff point where context can be lost, conflicting decisions can emerge, and orchestration cost grows. The tradeoff:

| Consideration | Favor splitting | Favor keeping combined |
|---|---|---|
| **Context window** | Role needs deep specialized context that would crowd out other concerns | Subroles share 80%+ of the same context |
| **Parallelism** | Work can genuinely happen concurrently | Tasks are sequential anyway |
| **Decision authority** | Distinct expertise drives different decisions | Same person would decide the same way |
| **Frequency** | Subrole is needed constantly | Subrole is needed occasionally |
| **Tool access** | Needs different tool sets | Same tools |

**Rule of thumb:** Split when the subrole needs its own persistent context and runs often enough to justify the coordination cost. Otherwise, make it a "mode" within the parent role (like how our Researcher can do proofs *and* literature gaps without being two agents).

**The real risk of over-splitting:** It's not token cost — it's *coherence*. When 20 agents each own a slice, nobody owns the whole picture. A Marketing agent that doesn't understand the product deeply will produce generic copy. An SEO agent disconnected from the content strategy will optimize for the wrong keywords. Subroles work best when they report *through* a parent role that maintains the holistic view.

---

## 2. Organizational Layers

### Layer 0: Strategy & Governance

These roles exist above day-to-day execution. They set direction and constraints.

| Role | Responsibility | Activates when |
|---|---|---|
| **CEO / Visionary** | Company mission, product vision, market positioning, kill/pivot decisions | Kickoff, major pivots, quarterly planning |
| **CTO / Chief Architect** | Tech strategy, build-vs-buy, platform bets, technical debt budget | Architecture decisions, scaling inflection points |
| **CPO / Product Strategist** | Product-market fit, user research synthesis, feature prioritization, competitive analysis | Roadmap planning, feature scoping |

> **Subrole consideration:** At small scale, CEO + CTO + CPO can be one "Strategy" agent. Split when the company has multiple products or when technical and product strategy genuinely diverge.

### Layer 1: Planning & Process (Agile)

This is the layer the first draft was missing. Real companies don't just have roles — they have *process* that coordinates those roles over time.

| Role | Responsibility | Activates when |
|---|---|---|
| **Scrum Master / Agile Coach** | Sprint planning, backlog grooming, velocity tracking, impediment removal, retrospectives | Every sprint boundary, when blockers arise |
| **Project Manager** | Cross-team dependencies, timeline management, resource allocation, risk tracking, stakeholder communication | Ongoing throughout project lifecycle |
| **Product Owner** | User stories, acceptance criteria, sprint backlog prioritization, stakeholder translation | Every sprint, feature kickoff |

#### How Agile maps to agents

| Agile ceremony | Agent equivalent |
|---|---|
| **Sprint planning** | Orchestrator decomposes next sprint's work into agent tasks with dependencies |
| **Daily standup** | Agents report status to Orchestrator; blockers get escalated or reassigned |
| **Sprint review / demo** | QA + Product Owner agents validate deliverables against acceptance criteria |
| **Retrospective** | Orchestrator analyzes what worked/failed, updates process rules for next sprint |
| **Backlog grooming** | Product Owner + Architect refine upcoming stories, add technical details |
| **Kanban board** | Task tracking system (TodoWrite/TaskCreate) with status columns |

> **Key insight:** Agile isn't just a human ritual — it's a coordination protocol. Agents need the same thing: defined cadence, prioritized backlog, acceptance gates, and feedback loops. The Orchestrator naturally fills the Scrum Master function, but a dedicated Project Manager role handles cross-team visibility that the Orchestrator (focused on task-level decomposition) might miss.

### Layer 2: Design & Creative

| Role | Responsibility | Possible subroles |
|---|---|---|
| **UX Designer** | User flows, wireframes, information architecture, usability testing | UX Researcher (user interviews, analytics interpretation) |
| **UI Designer** | Visual design, design system, component library, responsive layouts | Motion Designer (animations, transitions, microinteractions) |
| **Brand Designer** | Brand identity, tone, visual language consistency | — |
| **Copywriter** | Product copy, onboarding, error messages, help text | UX Writer (microcopy specialist) |

> **Subrole consideration:** UX + UI can stay combined for most projects. Split only when the product is complex enough that user research and visual execution need dedicated attention. Brand can fold into UI unless multiple products need distinct identities.

### Layer 3: Engineering

| Role | Responsibility | Possible subroles |
|---|---|---|
| **Frontend Engineer** | UI implementation, state management, client-side performance, accessibility | iOS Engineer, Android Engineer, Web Engineer (split by platform) |
| **Backend Engineer** | APIs, business logic, data modeling, integrations | API Engineer, Data Engineer, ML Engineer |
| **DevOps / SRE** | CI/CD, infrastructure-as-code, monitoring, alerting, incident response | Platform Engineer (internal tools, developer experience) |
| **Database Engineer** | Schema design, query optimization, migrations, backup strategy | — (often combined with Backend) |

> **Subrole consideration:** Platform-specific splits (iOS/Android/Web) are justified when the platforms have genuinely different codebases. If it's React Native or Flutter, keep it one role. Backend subroles split when the system has distinct domains (ML pipeline vs. REST API vs. data warehouse).

### Layer 4: Quality & Security

| Role | Responsibility | Possible subroles |
|---|---|---|
| **QA Engineer** | Test strategy, test automation, regression, exploratory testing, bug triage | Performance Tester, Accessibility Tester |
| **Security Engineer / Red Team** | Threat modeling, penetration testing, dependency audits, OWASP compliance | AppSec (code-level), InfraSec (cloud/network) |
| **Code Reviewer** | Code quality, architecture adherence, style consistency, knowledge sharing | — (often a mode of Senior Engineer) |

### Layer 5: Release & Distribution

| Role | Responsibility | Possible subroles |
|---|---|---|
| **Release Manager** | Versioning, changelogs, feature flags, rollout strategy, rollback plans | — |
| **App Store Manager** | Store listings, screenshots, A/B tests, review responses, compliance | ASO Specialist (App Store Optimization — keywords, metadata, conversion) |
| **Technical Writer** | API docs, user guides, migration guides, knowledge base | — |

### Layer 6: Growth & Marketing

| Role | Responsibility | Possible subroles |
|---|---|---|
| **Growth Lead** | Acquisition strategy, funnel optimization, experimentation, metrics | — |
| **Content Marketer** | Blog posts, case studies, social media, thought leadership | SEO Specialist, Social Media Manager |
| **Performance Marketer** | Paid campaigns, attribution, budget allocation, ad creative | — |
| **Community Manager** | User forums, feedback collection, beta programs, developer relations | — |

> **Subrole consideration:** SEO and ASO are strong candidates for dedicated subroles because they require deep specialized knowledge (keyword research, algorithm understanding, technical audits) that differs significantly from general marketing. An SEO subrole under Content Marketer and an ASO subrole under App Store Manager is a natural split.

### Layer 7: Post-Release Operations

| Role | Responsibility | Possible subroles |
|---|---|---|
| **Support Engineer** | Bug reproduction, user issue triage, hotfix coordination | L1 Support (first response), L2 Support (technical escalation) |
| **Analytics Engineer** | Event taxonomy, dashboards, funnel analysis, A/B test analysis | — |
| **On-Call / Incident Commander** | Production incidents, postmortems, SLA management | — (often a rotation across SRE + Backend) |

---

## 3. Orchestration Model

### Single Orchestrator vs. Hierarchical

For a full company simulation, a single flat Orchestrator breaks down. The span of control is too wide. Instead:

```
                        CEO / Visionary
                              |
              +---------------+---------------+
              |               |               |
            CTO            CPO         Growth Lead
              |               |               |
     +--------+--------+     |        +------+------+
     |        |        |     |        |      |      |
  Frontend Backend   DevOps  PO    Content  Perf  Community
     |        |              |     Marketer Marketer
    QA     Security     Scrum Master
```

**Each layer has its own orchestration:**
- **Strategic Orchestrator** (CEO level): quarterly goals, major pivots
- **Product Orchestrator** (CPO/PM level): sprint-level decomposition, cross-team dependencies
- **Technical Orchestrator** (CTO level): architecture decisions, technical debt allocation
- **Execution Orchestrators** (team leads): day-to-day task assignment within a team

### Communication patterns

| Pattern | When | Example |
|---|---|---|
| **Top-down directive** | Strategy → Execution | CEO decides to pivot; flows down to all teams |
| **Bottom-up escalation** | Execution → Strategy | Backend discovers a scaling limit; escalates to CTO |
| **Lateral handoff** | Peer → Peer | Designer hands mockups to Frontend Engineer |
| **Broadcast** | One → All | Release Manager announces code freeze |
| **Request-response** | Any → Any | Frontend asks Backend for API contract |

---

## 4. The Full Lifecycle

### Phase 1: Discovery & Strategy
```
CEO → CPO → UX Designer (research) → Architect
Output: Product brief, competitive analysis, technical feasibility
```

### Phase 2: Planning
```
CPO → Product Owner → Architect → Project Manager
Output: Roadmap, epic breakdown, sprint plan, architecture doc
```

### Phase 3: Design
```
UX Designer → UI Designer → Brand Designer → Copywriter
Output: Wireframes, mockups, design system, copy deck
Gate: Design review (CPO + QA sign-off)
```

### Phase 4: Build (Sprint cycles)
```
Sprint Planning (Scrum Master + PO + Engineers)
  → Parallel execution:
      ├─ Frontend ←→ Designer (iterate)
      ├─ Backend ←→ Architect (iterate)
      ├─ DevOps (infra, CI/CD)
      └─ QA (test automation, continuous testing)
  → Sprint Review → Retrospective → Next Sprint
Output: Working software increments
Gate: Sprint review (PO acceptance)
```

### Phase 5: Hardening
```
QA (regression, edge cases) + Security (audit, pen test) + Performance testing
Output: Bug fixes, security patches, performance optimizations
Gate: Release readiness review
```

### Phase 6: Launch
```
Release Manager (deploy) + App Store Manager (listing) + Content Marketer (launch content) + Growth Lead (campaigns)
Output: Live product, store listing, launch blog post, ad campaigns
Gate: Rollout metrics (crash rate, error rate, adoption)
```

### Phase 7: Operate & Grow
```
Ongoing parallel:
  ├─ Support Engineer (bug triage → feeds back to Sprint backlog)
  ├─ Analytics Engineer (dashboards, funnel analysis → feeds back to CPO)
  ├─ On-Call/SRE (incidents, uptime)
  ├─ Growth Lead (experiments, optimization)
  └─ Community Manager (feedback loops)
→ Feeds into next Phase 1 for the next major version
```

---

## 5. Role Count Analysis

### Minimal viable company (MVP — startup mode)
**8 roles** — maximum overlap, everyone wears multiple hats:

| Combined role | Covers |
|---|---|
| Strategist | CEO + CPO + Product Owner |
| Architect | CTO + Backend lead + Security |
| Designer | UX + UI + Brand |
| Frontend Engineer | + Copywriter (microcopy) |
| Backend Engineer | + Database + DevOps |
| QA Engineer | + Code Reviewer |
| Release & Growth | Release Manager + App Store + Marketing |
| Orchestrator | Scrum Master + PM + Incident Commander |

### Growth-stage company
**15-18 roles** — specialized where it matters most:

Split out: dedicated Security, dedicated DevOps, separate UX/UI, dedicated Growth Lead, dedicated PM, separate Content/Performance marketing.

### Full-scale company
**25-30 roles** including subroles:

All roles from Section 2, plus key subroles: ASO Specialist, SEO Specialist, iOS/Android/Web Engineers, ML Engineer, Platform Engineer, UX Researcher.

### Diminishing returns threshold

Beyond ~30 roles, coordination cost likely exceeds specialization benefit for an AI agent system. Unlike humans, agents don't have ego, meeting fatigue, or communication anxiety — but they *do* have:
- **Context loss at handoffs** (the biggest cost)
- **Conflicting decisions** between agents who don't share full context
- **Orchestration complexity** (O(n^2) potential interactions)
- **Redundant work** when role boundaries are ambiguous

---

## 6. Comparison to Our Academic System

| Dimension | Academic (Paper 1) | Tech Company |
|---|---|---|
| Roles | 10 | 8–30 depending on scale |
| Layers | 1 (flat) | 3–4 (hierarchical) |
| Parallelism | Low (mostly sequential) | High (many concurrent workstreams) |
| Sprint cadence | No (continuous) | Yes (1–2 week sprints) |
| Orchestration | Single Orchestrator | Hierarchical (strategic → product → technical → execution) |
| Feedback loops | Reviewer → Writer → Reviewer | Multiple: QA ↔ Eng, Designer ↔ Frontend, Users ↔ Product |
| Deployment | LaTeX compile + submit | CI/CD pipeline, staged rollout, monitoring |
| Post-release | Revisions from referees | Continuous: support, monitoring, growth, iteration |
| Missing in academic | Agile process, deployment, marketing, ongoing operations | — |
| Missing in tech | Deep theoretical rigor, formal proofs, peer review process | — |

---

## 7. Context Management

Subagents operate within finite context windows. Without active management, agents lose coherence on long tasks and the Orchestrator's context fills with verbose subagent output.

### Subagent return summaries

When a subagent completes its task, it returns a summary to the Orchestrator — not its full output. Target 1,000-2,000 tokens covering:
- What was accomplished (deliverables produced, commits made, PRs opened)
- Key decisions made and why
- Blockers or escalations
- What the next agent in the pipeline needs to know

The Orchestrator should instruct subagents to end with a structured summary in their spawning template.

### PROJECT_STATE.md budget

Keep `PROJECT_STATE.md` under 500 tokens. The Orchestrator is responsible for compacting it after each sprint — archiving completed tickets, collapsing resolved decisions, and keeping only the currently actionable state. A bloated `PROJECT_STATE.md` wastes context in every agent session.

### Compaction strategy

When a task requires more work than fits in one context window:
1. The agent commits its in-progress work
2. Updates `PROJECT_STATE.md` with what was done and what remains
3. The Orchestrator spawns a fresh agent to continue from where the previous one stopped

This is cheaper than trying to compress a single long session.

### Per-role context loading

Not every agent needs to read every file. Load only what's necessary.

| Role | Must load | On-demand |
|------|-----------|-----------|
| Architect | PROJECT_STATE.md, architecture docs/ADRs | Source code (for review) |
| Product Owner | PROJECT_STATE.md, user stories, backlog | Design specs, technical docs |
| Designer | PROJECT_STATE.md, design system docs | User stories (for context) |
| Copywriter | PROJECT_STATE.md, voice/tone guide | Design specs (for context) |
| Frontend Engineer | PROJECT_STATE.md, design specs, API contracts | Backend code (for context) |
| Backend Engineer | PROJECT_STATE.md, API contracts, schema files | Frontend code (for context) |
| DevOps | PROJECT_STATE.md, infra config, CI/CD config | Application code (for debugging) |
| QA Engineer | PROJECT_STATE.md, user stories, acceptance criteria | Source code (for edge case analysis) |
| Security Engineer | PROJECT_STATE.md, auth/security code, dependencies | Full codebase (for audit) |
| Code Reviewer | PROJECT_STATE.md, PR diff, surrounding code | Architecture docs (for pattern check) |
| Release Manager | PROJECT_STATE.md, changelog, version files | CI/CD config (for deploy) |
| Technical Writer | PROJECT_STATE.md, API code, existing docs | User stories (for feature context) |
| Growth Lead | PROJECT_STATE.md, product docs, analytics | Competitor research (web search) |
| Support Engineer | PROJECT_STATE.md, logs, error reports | Source code (for root cause) |

---

## 8. Evaluation Patterns

### Rubric scoring

Use structured rubrics to evaluate deliverable quality at sprint review and release gates. Each dimension is scored 0.0-1.0:

| Dimension | What it measures | Scored by |
|-----------|-----------------|-----------|
| **Correctness** | Code works as specified, tests pass, no regressions | QA Engineer + Code Reviewer |
| **Security** | No vulnerabilities, auth/authz correct, dependencies clean | Security Engineer |
| **Architecture adherence** | Follows established patterns, no accidental complexity | Architect + Code Reviewer |
| **Performance** | Meets latency/throughput targets, no resource leaks | QA Engineer + DevOps |
| **UX quality** | Matches design spec, accessible, responsive | Designer + QA Engineer |
| **Documentation** | API docs current, user guides updated, README accurate | Technical Writer |
| **Test coverage** | Critical paths tested, edge cases covered, tests meaningful | QA Engineer + Code Reviewer |

### Decision thresholds

| Aggregate score | Action |
|----------------|--------|
| >= 0.8 | Ready to release — proceed to deployment |
| 0.6 - 0.8 | Targeted fixes — identify lowest-scoring dimensions, assign specific agents |
| < 0.6 | Sprint extension or scope reduction — escalate to Product Owner for re-prioritization |

### End-state evaluation

Judge outputs, not process. A feature that took three review cycles but scores 0.9 is better than one that passed on the first try but scores 0.6. The Orchestrator should not penalize agents for needing iteration — iteration is the mechanism by which quality improves.

### LLM-as-judge for comparing approaches

When the Architect proposes multiple solutions:
1. Have each approach prototyped (or described in detail)
2. Score independently on the relevant dimensions (performance, maintainability, security, complexity)
3. Pick the higher-scoring approach
4. Document the comparison in the ADR

---

## 9. Open Questions

1. **Agent memory sharing:** How much context should agents share? Full transparency (everyone sees everything) vs. need-to-know (reduces noise but risks silos)?
2. **Decision authority:** When two agents disagree (Designer wants X, Engineer says it's infeasible), who breaks the tie? Need explicit escalation paths.
3. **Quality vs. speed:** More agents = more parallelism = faster, but also more integration risk. What's the sweet spot?
4. **Cost model:** Each agent consumes tokens. A 30-agent system running sprint ceremonies could be expensive. Where does the ROI curve flatten?
5. **Stateful vs. stateless agents:** Should agents persist across sprints (remembering past decisions) or start fresh each time (avoiding stale assumptions)?
6. **Human-in-the-loop:** Where are the mandatory human checkpoints? Strategy? Design approval? Release sign-off? All of them?
