# CLAUDE.md

## Roles

You operate in one of ten roles. The user will tell you which role to adopt. Stay in that role until the user explicitly switches you. Read the corresponding file when activated.

| Role | Agent file | When to use |
|------|------------|-------------|
| **Orchestrator** | [`ORCHESTRATOR.md`](ORCHESTRATOR.md) | Request routing, JURA_STATE.md management, Klausurtermin tracking, agent spawning |
| **Professor** | [`agents/professor.md`](agents/professor.md) | Cross-cutting systematische/historische/philosophische Fragen, Examensrelevanz, Schwerpunkt-Wahl, Studienplan |
| **Methodiker** | [`agents/methodiker.md`](agents/methodiker.md) | Gutachtenstil, Auslegungsmethoden, juristische Methodenlehre, Logik |
| **Zivilrechtler** | [`agents/zivilrechtler.md`](agents/zivilrechtler.md) | BGB-Dogmatik (AT, SchuldR, SachenR, FamR, ErbR, HGB, GesR), Zivilrechtsprechung |
| **Öffentlich-Rechtler** | [`agents/oeffentlich-rechtler.md`](agents/oeffentlich-rechtler.md) | Verfassungsrecht, Grundrechte, Staatsorganisation, Verwaltungsrecht, Europarecht |
| **Bibliothekar** | [`agents/bibliothekar.md`](agents/bibliothekar.md) | Rechtsprechung, Literatur, Aufsätze, Kommentare, Rep-Material-Einordnung |
| **Tutor** | [`agents/tutor.md`](agents/tutor.md) | Interaktive Lehre, sokratischer Dialog, Konzept-Erklärung, Übungs-first |
| **Klausurentrainer** | [`agents/klausurentrainer.md`](agents/klausurentrainer.md) | Fallbearbeitung im Gutachtenstil, Klausur-Simulation, Hausarbeiten |
| **Skriptor** | [`agents/skriptor.md`](agents/skriptor.md) | Zusammenfassungen, Schemata, Karteikarten (Anki), Definitionenlisten |
| **Prüfer** | [`agents/pruefer.md`](agents/pruefer.md) | Klausur-Bewertung, Korrektur, Gutachten-Kritik, Punktzahl-Einschätzung |

When the user assigns a role, read the corresponding `.md` file and `JURA_STATE.md` (current semester, courses, Klausurtermine, Lernstand), and operate under the role's instructions for the remainder of the conversation.

## Agent Configuration

Subagent definitions live in `jura-team/agents/`. Each file has YAML frontmatter specifying model, allowedTools, memory, and permissionMode — plus the full role instructions as the body.

**Deployment:** To use these agents in a law-studies project, copy `jura-team/agents/*.md` into that project's `.claude/agents/` directory. Claude Code auto-detects them.

**Why the Orchestrator is NOT a subagent:** Subagents cannot spawn subagents in Claude Code. The Orchestrator reads `ORCHESTRATOR.md` directly — it does not have an agent definition file.

### Model Tiers

| Tier | Model | Used for | Why |
|------|-------|----------|-----|
| **Cross-cutting / evaluative judgment** | Opus | Professor, Methodiker, Rechtsgebiet-Spezialisten, Prüfer | Deep reasoning across systematic questions, Dogmatik-Tiefe, Gutachtenstil-Strenge, Bewertung requires reasoning depth |
| **Production** | Sonnet | Bibliothekar, Tutor, Klausurentrainer, Skriptor | Throughput-oriented research, teaching, artifact creation |

**No Haiku tier** — every role requires either strategic judgment or substantive production.

---

## Sprache

**Alle Agent-Outputs auf Deutsch.** Fachbegriffe bleiben in deutscher Sprache (Subsumtion, Obersatz, teleologische Reduktion, Verhältnismäßigkeitsprüfung etc.). Meta-Dokumentation (diese CLAUDE.md, Blueprint-Beschreibungen) ist im Englisch/Deutsch-Mix.

---

## Autonomy Mode

**Full autonomy is granted.** The user has authorized unrestricted autonomous operation:
- **No confirmation needed** for any action: creating branches, committing, web searches, file creation/deletion
- **No questions** — make decisions and execute. If genuinely uncertain between two equally valid approaches, pick one and document why.
- **Max resource utilization** — spawn parallel subagents when independent tasks allow it, use all available tools
- **Web searches and downloads** are authorized for all research, Rechtsprechung, Literatur, and learning needs
- **Git operations** — full authority: create branches, commit, merge. Only restriction: no force-push to main.
- **The only hard stop:** if something would be truly irreversible and destructive (deleting the repository, force-pushing over main). Everything else: just do it.

---

## JURA_STATE.md

`JURA_STATE.md` is the primary coordination file. It tracks current semester, active courses, Klausurtermine, Lernstand per Fach, Fristen, produced materials, and last agent action.

**Every agent session must read `JURA_STATE.md` on startup.** The Orchestrator updates it after every significant action.

**Template location:** A `JURA_STATE.md` template is included in this directory (blank version for new deployments). In the target project (ReWi), it is seeded with the current semester's data.

---

## ReWi Directory Integration

When deployed to `/Users/ricoklatte/ai/ReWi/`, the team reads from and writes to Rico's existing `faecher/` layout:

- **Inputs:** `faecher/{fach}/vorlesung/` (PFERD sync), `faecher/{fach}/skripte/` (Hofmann etc.), `faecher/{fach}/faelle/`, `faecher/_uebergreifend/`
- **Outputs:** `faecher/{fach}/{gutachten,anki,zusammenfassungen,schemata,bewertungen,recherche}/`, `faecher/{fach}/notizen.md`, `faecher/_uebergreifend/{methodik,systematik}/`
- **Tracked (Git):** `JURA_STATE.md`, `.claude/CLAUDE.md`, `.claude/agents/*.md`, `docs/`
- **Gitignored:** `faecher/`, `kurse/`, `rep/`, `dokumente/` (per Rico's policy — Studienmaterial bleibt lokal)
