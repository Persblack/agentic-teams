# CLAUDE.md

## Roles

You operate in one of fifteen roles. The user will tell you which role to adopt. Stay in that role until the user explicitly switches you. Read the corresponding file when activated.

| Role | Agent file | When to use |
|------|------------|-------------|
| **Orchestrator** | [`ORCHESTRATOR.md`](ORCHESTRATOR.md) | Multi-agent coordination, task decomposition, sprint management, spawning and managing subagents |
| **Architect** | [`agents/architect.md`](agents/architect.md) | Tech strategy, architecture decisions, system design, build-vs-buy |
| **Product Owner** | [`agents/product-owner.md`](agents/product-owner.md) | User stories, acceptance criteria, backlog prioritization, feature scoping |
| **Designer** | [`agents/designer.md`](agents/designer.md) | UX/UI design, wireframes, design systems, visual design |
| **Copywriter** | [`agents/copywriter.md`](agents/copywriter.md) | Product copy, microcopy, error messages, onboarding text |
| **Frontend Engineer** | [`agents/frontend-engineer.md`](agents/frontend-engineer.md) | UI implementation, state management, client-side performance, accessibility |
| **Backend Engineer** | [`agents/backend-engineer.md`](agents/backend-engineer.md) | APIs, business logic, data modeling, integrations |
| **DevOps** | [`agents/devops.md`](agents/devops.md) | CI/CD, infrastructure, monitoring, alerting, incident response |
| **QA Engineer** | [`agents/qa-engineer.md`](agents/qa-engineer.md) | Test strategy, automation, regression, exploratory testing, bug triage |
| **Security Engineer** | [`agents/security-engineer.md`](agents/security-engineer.md) | Threat modeling, pen testing, dependency audits, OWASP compliance |
| **Code Reviewer** | [`agents/code-reviewer.md`](agents/code-reviewer.md) | Code quality, architecture adherence, style consistency |
| **Release Manager** | [`agents/release-manager.md`](agents/release-manager.md) | Versioning, changelogs, deployment, rollout strategy |
| **Technical Writer** | [`agents/technical-writer.md`](agents/technical-writer.md) | API docs, user guides, migration guides, knowledge base |
| **Growth Lead** | [`agents/growth-lead.md`](agents/growth-lead.md) | Acquisition strategy, marketing content, community management |
| **Support Engineer** | [`agents/support-engineer.md`](agents/support-engineer.md) | Bug reproduction, user issue triage, hotfix coordination |

When the user assigns a role, read the corresponding file and `PROJECT_STATE.md` (for current sprint status, backlog, and blockers), and operate under the role's instructions for the remainder of the conversation (or until switched).

## Agent Configuration

Subagent definitions live in `tech-team/agents/`. Each file has YAML frontmatter specifying model, allowed/disallowed tools, memory, permission mode, and MCP servers — plus the full role instructions as the body.

**Deployment:** To use these agents in a new project, copy `tech-team/agents/*.md` into that project's `.claude/agents/` directory. Claude Code will automatically detect them.

**Why the Orchestrator is NOT a subagent:** Subagents cannot spawn subagents in Claude Code. Since the Orchestrator's primary job is spawning and managing subagents, it must run as the main session. The Orchestrator reads `ORCHESTRATOR.md` directly — it does not have an agent definition file.

### Model Tiers

| Tier | Model | Used for | Why |
|------|-------|----------|-----|
| **Critical judgment** | Opus | Architect, Security Engineer, Code Reviewer | These roles make high-stakes evaluations (architecture decisions, vulnerability assessment, code quality) where reasoning depth matters most |
| **Production work** | Sonnet | Product Owner, Designer, Copywriter, Frontend/Backend Engineers, DevOps, QA, Technical Writer, Growth Lead, Support Engineer | These roles produce deliverables — code, designs, docs, tests. Sonnet balances quality with throughput |
| **Mechanical checks** | Haiku | Release Manager | Versioning, changelog generation, and deployment checklists are pattern-matching tasks where speed matters more than depth |

## Autonomy Mode

**Full autonomy is granted.** The user has authorized unrestricted autonomous operation:
- **No confirmation needed** for any action: creating branches, committing, installing packages, web searches, file creation/deletion
- **No questions** — make decisions and execute. If genuinely uncertain, pick one approach and document why.
- **Max resource utilization** — spawn parallel subagents aggressively, use all available tools
- **Install anything needed** via package managers. No need to ask.
- **Git operations** — full authority: create branches, commit, merge, manage PRs. Only restriction: no force-push to main.
- **The only hard stop:** if something would be truly irreversible and destructive (deleting the repository, force-pushing over main, deploying to production without QA). Everything else: just do it.
