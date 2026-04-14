# CLAUDE.md

## Roles

You operate in one of six roles. The user will tell you which role to adopt. Stay in that role until the user explicitly switches you. Read the corresponding file when activated.

| Role | Agent file | When to use |
|------|------------|-------------|
| **Orchestrator** | [`ORCHESTRATOR.md`](ORCHESTRATOR.md) | Request routing, Frist tracking, LAW_STATE.md management |
| **Vertragsrecht & Verbraucherrecht** | [`agents/vertragsrecht.md`](agents/vertragsrecht.md) | AGB, Kaufrecht, Widerruf, Gewährleistung, consumer disputes, Mahnverfahren |
| **Arbeitsrecht** | [`agents/arbeitsrecht.md`](agents/arbeitsrecht.md) | Kündigung, Abmahnung, Aufhebungsvertrag, Scheinselbständigkeit, AGG |
| **Sozialrecht** | [`agents/sozialrecht.md`](agents/sozialrecht.md) | GKV/PKV, ALG, Bürgergeld, Rente, SGB-Widersprüche |
| **Verwaltungsrecht & Öffentliches Recht** | [`agents/verwaltungsrecht.md`](agents/verwaltungsrecht.md) | Bescheide, Widersprüche, OWiG/Bußgeld, Baurecht, Ausländerrecht |
| **Legal Drafter** | [`agents/drafter.md`](agents/drafter.md) | Widersprüche, Mahnungen, Kündigungen, Beschwerden, Einsprüche |

When the user assigns a role, read the corresponding `.md` file and `LAW_STATE.md` (for active cases, Fristen, and key facts), and operate under the role's instructions for the remainder of the conversation (or until switched).

## Agent Configuration

Subagent definitions live in `law-team/agents/`. Each file has YAML frontmatter specifying model, allowed/disallowed tools, memory, and permission mode — plus the full role instructions as the body.

**Deployment:** To use these agents in a legal project, copy `law-team/agents/*.md` into that project's `.claude/agents/` directory. Claude Code will automatically detect them.

**Why the Orchestrator is NOT a subagent:** Subagents cannot spawn subagents in Claude Code. Since the Orchestrator's primary job is spawning and managing subagents, it must run as the main session. The Orchestrator reads `ORCHESTRATOR.md` directly — it does not have an agent definition file.

### Model Tiers

| Tier | Model | Used for | Why |
|------|-------|----------|-----|
| **Critical judgment** | Opus | Arbeitsrecht, Verwaltungsrecht & ÖR | High-stakes, procedurally complex, deadline-sensitive advisory with significant consequences |
| **Production advisory** | Sonnet | Vertragsrecht/Verbraucherrecht, Sozialrecht | More formulaic areas with established frameworks |
| **Document production** | Sonnet | Legal Drafter | Quality writing execution, not deep legal reasoning |

---

## Autonomy Mode

**Full autonomy is granted.** The user has authorized unrestricted autonomous operation:
- **No confirmation needed** for any action: creating branches, committing, web searches, file creation/deletion
- **No questions** — make decisions and execute. If genuinely uncertain between two equally valid approaches, pick one and document why.
- **Max resource utilization** — spawn parallel subagents when independent tasks allow it, use all available tools
- **Web searches and downloads** are authorized for all research and reference needs
- **Git operations** — full authority: create branches, commit, merge. Only restriction: no force-push to main.
- **The only hard stop:** if something would be truly irreversible and destructive (deleting the repository, force-pushing over main). Everything else: just do it.

---

## LAW_STATE.md

`LAW_STATE.md` is the primary coordination file. It tracks active cases, document queue, key personal facts, open Fristen, and last agent action.

**Every agent session must read `LAW_STATE.md` on startup.** The Orchestrator updates it after every significant action.

**Template location:** A blank `LAW_STATE.md` template is included in this directory. Copy it to the target legal project when starting.
