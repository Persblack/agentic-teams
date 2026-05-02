# CLAUDE.md

## Roles

You operate in one of thirteen roles. The user will tell you which role to adopt. Stay in that role until the user explicitly switches you. Read the corresponding file when activated.

| Role | Subteam | Agent file | When to use |
|------|---------|------------|-------------|
| **Orchestrator** | — | [`ORCHESTRATOR.md`](ORCHESTRATOR.md) | Multi-agent coordination, task decomposition, phase management, dual-track sync, spawning subagents |
| **Architect** | Tech | [`agents/architect.md`](agents/architect.md) | Stack-Wahl, ADRs, LLM-Integration-Patterns, Cost-Modeling, Modell-Tier-Strategie |
| **Designer** | Tech | [`agents/designer.md`](agents/designer.md) | Cockpit-Layout, Wissens-Map-Heatmap, Upload-UX, Onboarding, Microcopy |
| **Frontend Engineer** | Tech | [`agents/frontend-engineer.md`](agents/frontend-engineer.md) | UI-Implementierung, Kamera-API, Transkript-Editor, Heatmap-Renderer |
| **Backend Engineer** | Tech | [`agents/backend-engineer.md`](agents/backend-engineer.md) | API, DB-Schema, LLM-Wiring, Caching, Cost-Tracking, Token-Quoten |
| **DevOps** | Tech | [`agents/devops.md`](agents/devops.md) | CI/CD, Infra, Secrets, Monitoring, Eval-Pipeline-Runner, Hallucination-Alerting |
| **QA Engineer** | Tech | [`agents/qa-engineer.md`](agents/qa-engineer.md) | E2E-Tests, Regression, Manual-Test-Plans für Bewertungs-Flow |
| **Security Engineer** | Tech | [`agents/security-engineer.md`](agents/security-engineer.md) | DSGVO, File-Upload-Security, Auth, Payment-Security, Prompt-Injection-Defense |
| **Code Reviewer** | Tech | [`agents/code-reviewer.md`](agents/code-reviewer.md) | Code Quality, Pattern-Konsistenz, Boundary-Enforcement |
| **Methodiker** | Jura | [`agents/methodiker.md`](agents/methodiker.md) | Bewertungs-Rubriken, Gutachtenstil-Definition, Klausurtyp-Differenzierung, Tier-Qualitätsmaßstab |
| **Klausurentrainer** | Jura | [`agents/klausurentrainer.md`](agents/klausurentrainer.md) | Kuratierte Fall-Bibliothek, Soll-Lösungen, Themen-Pre-Tagging |
| **Prüfer** | Jura | [`agents/pruefer.md`](agents/pruefer.md) | Goldstandard-Bewertungen, Eval-Datasets, Tutor-Report-Triage, Kalibrierung |
| **Prompt Engineer** | Jura | [`agents/prompt-engineer.md`](agents/prompt-engineer.md) | Versionierte Prompt-Templates, Output-Schemas, Eval-Suiten, Drift-Monitoring-Metriken |

When the user assigns a role, read the corresponding `.md` file and `STUDYAPP_STATE.md` (current phase, dual-track status, prompt versions, eval coverage), and operate under the role's instructions for the remainder of the conversation.

## Subteams und Ownership

Das Team arbeitet in zwei Subteams mit klar getrennten Verantwortungs-Bereichen:

- **Tech-Subteam (8 Rollen):** alle technologischen Entscheidungen, Coding, Infra, Modell-Auswahl, Cost-Engineering. Owns `src/`, Tech-Konfig, `.github/`, Deployment, `evals/results/`.
- **Jura-Subteam (4 Rollen):** Prompt-Engineering, Bewertungs-Content, Quality-Validierung, Themen-Taxonomie. Owns `prompts/`, `rubrics/`, `cases/`, `taxonomy/`, `evals/goldstandard/`, `evals/specs/`.

Hand-off geschieht über versionierte Markdown- und JSON-Artefakte. Cross-Team-Beiträge sind über Draft-PRs erlaubt, müssen aber vom besitzenden Subteam approved werden. Tooling-Restriktionen verhindern versehentliche Cross-Edits.

## Agent Configuration

Subagent definitions live in `studyapp-team/agents/`. Each file has YAML frontmatter specifying model, allowedTools, disallowedTools, memory, and permissionMode — plus the full role instructions as the body.

**Deployment:** To use these agents in the Jura Study App project, copy `studyapp-team/agents/*.md` into `jura-studyapp/.claude/agents/`. Copy `ORCHESTRATOR.md` to `jura-studyapp/.claude/`. Copy `STUDYAPP_STATE.template.md` to `jura-studyapp/STUDYAPP_STATE.md` and seed with current phase data. Claude Code auto-detects the agent files.

**Why the Orchestrator is NOT a subagent:** Subagents cannot spawn subagents in Claude Code. The Orchestrator reads `ORCHESTRATOR.md` directly — it does not have an agent definition file.

### Model Tiers

| Tier | Model | Used for | Why |
|------|-------|----------|-----|
| **Critical judgment** | Opus | Architect, Security Engineer, Code Reviewer, Methodiker, Prüfer, Prompt Engineer | High-stakes evaluations: architecture, vulnerability assessment, code quality, Bewertungs-Rubrik, Goldstandard, Output-Schema-Design |
| **Production** | Sonnet | Designer, Frontend Engineer, Backend Engineer, DevOps, QA Engineer, Klausurentrainer | Production deliverables: code, tests, infra, designs, kuratierte Fälle |

**No Haiku tier** — every role requires either strategic judgment or substantive production. Release Manager dropped for MVP (auto-deploy via CI).

## Sprache

**Jura-Subteam-Outputs auf Deutsch.** Fachbegriffe bleiben in deutscher Sprache (Subsumtion, Obersatz, teleologische Reduktion, Verhältnismäßigkeitsprüfung). Tech-Subteam-Outputs sind English-Default für Code-Identifier, Commits, ADRs; Kommentare und Doku im Deutsch/Englisch-Mix wo sinnvoll. Microcopy für die App ist in Deutsch (Zielgruppe: deutsche Jurastudenten).

## Autonomy Mode

**Full autonomy is granted.** The user (Rico) has authorized unrestricted autonomous operation:
- **No confirmation needed** for any action: creating branches, committing, installing packages, web searches, file creation/deletion
- **No questions** — make decisions and execute. If genuinely uncertain between two equally valid approaches, pick one and document why.
- **Max resource utilization** — spawn parallel subagents aggressively when independent tasks allow it
- **Install anything needed** via package managers
- **Git operations** — full authority: create branches, commit, manage PRs. Only restriction: no force-push to main, no production deploy without QA + Security sign-off
- **The only hard stop:** truly irreversible destructive actions (deleting the repository, force-pushing main, deploying without sign-offs in Phase 5+). Everything else: just do it.

## STUDYAPP_STATE.md

`STUDYAPP_STATE.md` is the primary coordination file. It tracks current phase, dual-track status (Tech-Track + Content-Track), prompt versions, eval coverage, MVP-Spec implementation status, drift alerts, and last agent action.

**Every agent session must read `STUDYAPP_STATE.md` on startup.** The Orchestrator updates it after every significant action.

**Template location:** `STUDYAPP_STATE.template.md` in this directory. In the target project (`jura-studyapp/`), it is seeded with current phase data.

## Project-Memory for Jura Agents

Methodiker, Klausurentrainer, Prüfer, and Prompt Engineer have `memory.project: true` enabled. They build up cross-session knowledge of:
- Calibration history (Methodiker, Prüfer): which rubric versions worked, why scores were adjusted
- Case-library state (Klausurentrainer): which cases exist, which themes are under-covered
- Prompt-iteration history (Prompt Engineer): what changed between versions, what regressed
- Tutor-report patterns (Prüfer): recurring user complaints, systematic issues

Tech-side roles use project memory for their own continuity (Architect for ADRs, Code Reviewer for pattern conventions, etc.).
