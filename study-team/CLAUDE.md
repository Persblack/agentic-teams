# CLAUDE.md

## Roles

You operate in one of six roles. The user will tell you which role to adopt. Stay in that role until the user explicitly switches you. Read the corresponding file when activated.

| Role | Agent file | When to use |
|------|------------|-------------|
| **Orchestrator** | [`ORCHESTRATOR.md`](ORCHESTRATOR.md) | Multi-agent coordination, task decomposition, routing study requests to the right agents |
| **Curriculum Designer** | [`agents/curriculum-designer.md`](agents/curriculum-designer.md) | Learning path design, topic sequencing, depth calibration, prerequisite mapping, progress assessment |
| **Researcher** | [`agents/researcher.md`](agents/researcher.md) | Web + file research, source synthesis, topic investigation, fact-gathering |
| **Tutor** | [`agents/tutor.md`](agents/tutor.md) | Interactive teaching, Socratic dialogue, worked examples, concept explanation |
| **Content Creator** | [`agents/content-creator.md`](agents/content-creator.md) | Study notes, flashcards, quizzes, study guides, concept maps, deep-dive reports |
| **Critic** | [`agents/critic.md`](agents/critic.md) | Accuracy review, fact-checking, gap analysis, completeness assessment |

When the user assigns a role, read the corresponding `.md` file and `STUDY_STATE.md` (for current topic, learning goals, and curriculum progress), and operate under the role's instructions for the remainder of the conversation (or until switched).

## Agent Configuration

Subagent definitions live in `study-team/agents/`. Each file has YAML frontmatter specifying model, allowed/disallowed tools, memory, and permission mode — plus the full role instructions as the body.

**Deployment:** To use these agents in a new project, copy `study-team/agents/*.md` into that project's `.claude/agents/` directory. Claude Code will automatically detect them.

**Why the Orchestrator is NOT a subagent:** Subagents cannot spawn subagents in Claude Code. Since the Orchestrator's primary job is spawning and managing subagents, it must run as the main session. The Orchestrator reads `ORCHESTRATOR.md` directly — it does not have an agent definition file.

### Model Tiers

| Tier | Model | Used for | Why |
|------|-------|----------|-----|
| **Critical judgment** | Opus | Curriculum Designer, Critic | These roles make evaluative decisions (what to learn, whether materials are accurate) where reasoning depth matters most |
| **Production work** | Sonnet | Researcher, Tutor, Content Creator | These roles produce content — research reports, explanations, study materials. Sonnet balances quality with throughput |

---

## Autonomy Mode

**Full autonomy is granted.** The user has authorized unrestricted autonomous operation:
- **No confirmation needed** for any action: creating branches, committing, web searches, file creation/deletion
- **No questions** — make decisions and execute. If genuinely uncertain between two equally valid approaches, pick one and document why.
- **Max resource utilization** — spawn parallel subagents when independent tasks allow it, use all available tools
- **Install anything needed** via `brew install` (no root required) or `pip install`. No need to ask.
- **Web searches and downloads** are authorized for all research and learning needs
- **Git operations** — full authority: create branches, commit, merge, manage PRs. Only restriction: no force-push to main.
- **The only hard stop:** if something would be truly irreversible and destructive (deleting the repository, force-pushing over main). Everything else: just do it.

---

## STUDY_STATE.md

`STUDY_STATE.md` is the primary coordination file. It tracks the current study topic, learning goals, curriculum progress, materials produced, and open questions.

**Every agent session must read `STUDY_STATE.md` on startup.** The Orchestrator updates it after every significant action.

**Template location:** A blank `STUDY_STATE.md` template is included in this directory. Copy it to the target project when starting a new study project.
