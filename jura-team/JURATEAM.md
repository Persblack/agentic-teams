# AI Jura Team — Jurastudium FAU Blueprint

> **Purpose:** Model a personal Jurastudium support team as specialized AI agents. Covers the full German legal education pipeline — daily learning, material production, Fallbearbeitung, Klausurenvorbereitung, cross-cutting systematic questions, Hausarbeiten, and long-range Studienplanung — from 1. Fachsemester through Staatsexamen.

---

## 1. Design Principles

### Why a Jura team?

German Jurastudium is unlike other disciplines: it combines Rechtsdogmatik (deep systematic knowledge of three distinct Rechtsgebiete), Methodenlehre (Gutachtenstil as core argumentation), Fallbearbeitung (case-based learning), and a 5-9 year horizon culminating in Staatsexamen. No single cognitive mode captures all of this.

The Jura team separates these modes. Dogmatik-Spezialisten go deep in their Rechtsgebiet. The Methodiker enforces Gutachtenstil-Disziplin. The Klausurentrainer works cases under exam conditions. The Prüfer evaluates retrospectively. The Professor holds the system together. Each role has distinct expertise, tools, and decision authority.

### Key design choices

| Choice | Rationale |
|---|---|
| **Extensible specialists** | Three Rechtsgebiete are deeply distinct. Add per Studienplan — Zivilrechtler + Öffentlich-Rechtler now, Strafrechtler when StGB starts |
| **Stable supporting core** | 6 supporting roles (Professor, Methodiker, Bibliothekar, Tutor, Klausurentrainer, Skriptor, Prüfer) always active — each is a distinct cognitive mode |
| **Gutachtenstil-centric** | Methodiker enforces Gutachtenstil across all outputs. The non-negotiable skill of German law |
| **German output default** | All agent work in German. Fachbegriffe untranslated. Matches native language and legal conventions |
| **Respects existing ReWi setup** | Uses `faecher/` layout, PFERD sync, Anki workflow, Notion hub. No restructuring |
| **No phase pipeline** | Jurastudium is long-horizon with ad-hoc requests. Flexible routing matches actual workflow |
| **Two model tiers only** | Opus (judgment) + Sonnet (production). No Haiku — no purely mechanical roles |

---

## 2. The Ten Roles

### Layer 0: Coordination
| Role | File | Core function |
|---|---|---|
| **Orchestrator** | `ORCHESTRATOR.md` | Request routing, agent spawning, JURA_STATE.md management, Klausurtermin tracking, Notion sync |

### Layer 1: Strategie & Methode (Opus)
| Role | File | Core function |
|---|---|---|
| **Professor** | `agents/professor.md` | Cross-cutting systematische/historische/philosophische Fragen, Examensrelevanz, Studienplan, Schwerpunkt-Wahl (später) |
| **Methodiker** | `agents/methodiker.md` | Gutachtenstil-Disziplin, Auslegungsmethoden, juristische Methodenlehre, Logik |

### Layer 2: Rechtsgebiet-Spezialisten (Opus) — extensible
| Role | File | Core function | Status |
|---|---|---|---|
| **Zivilrechtler** | `agents/zivilrechtler.md` | BGB-Dogmatik (alle Teile nach Bedarf) | **aktiv** |
| **Öffentlich-Rechtler** | `agents/oeffentlich-rechtler.md` | Verfassungsrecht, Grundrechte, Staatsorganisation, Verwaltungsrecht, Europarecht | **aktiv** |
| **Strafrechtler** | *(add later)* | StGB AT & BT, StPO | bei StGB-Kurs |
| *Further specialists* | *(add later)* | ArbR, IPR, IT-Recht, Steuerrecht etc. | per Studienplan |

### Layer 3: Produktion (Sonnet)
| Role | File | Core function |
|---|---|---|
| **Bibliothekar** | `agents/bibliothekar.md` | Rechtsprechung (BVerfG/BGH/BVerwG), Literatur, Aufsätze, Kommentare, Rep-Material |
| **Tutor** | `agents/tutor.md` | Interaktive Lehre, sokratischer Dialog, Übungs-first-Ansatz |
| **Klausurentrainer** | `agents/klausurentrainer.md` | Fallbearbeitung im Gutachtenstil, Klausur-Simulation, Hausarbeiten |
| **Skriptor** | `agents/skriptor.md` | Vorlesungs-Zusammenfassungen, Schemata, Karteikarten (Anki), Definitionenlisten |

### Layer 4: Qualität (Opus)
| Role | File | Core function |
|---|---|---|
| **Prüfer** | `agents/pruefer.md` | Bewertet Klausur-Lösungen und Hausarbeiten wie ein Klausurenkorrektor, Punktzahl-Einschätzung |

---

## 3. Orchestration Model

### Single flat Orchestrator
- Lower parallelism — study work is mostly sequential
- Manageable span of control — 10 roles mit klaren Routing-Regeln
- Single learner — eine Person, keine Multi-User-Koordination
- Ad-hoc request flow — keine festen Phasen

### Flexible routing

| Request | Routing |
|---|---|
| "Erklär mir Willenserklärung" | Tutor + Zivilrechtler |
| "Schema zu § 119 BGB" | Skriptor + Zivilrechtler |
| "BGB-AT-Fall lösen" | Klausurentrainer + Zivilrechtler |
| "Korrigiere meine Klausur" | Prüfer + relevante:r Spezialist:in |
| "BGH-Urteile zu X" | Bibliothekar |
| "Wie funktioniert teleologische Reduktion?" | Methodiker |
| "Verhältnis Grundrechte ↔ Privatrecht" | Professor + Methodiker |
| "Hausarbeit zu X" | Bibliothekar → Klausurentrainer → Methodiker → Prüfer |
| "Fasse Vorlesung vom 14.4. zusammen" | Skriptor (liest faecher/bgb-at/vorlesung/) |
| "Was sind meine Fristen?" | Orchestrator (JURA_STATE) |

---

## 4. Model Tier Allocation

| Tier | Model | Roles | Rationale |
|---|---|---|---|
| **Cross-cutting / evaluative judgment** | Opus | Professor, Methodiker, Rechtsgebiet-Spezialisten, Prüfer | Deep reasoning across systematic questions, Dogmatik-Tiefe, Gutachtenstil-Strenge, Bewertung |
| **Production** | Sonnet | Bibliothekar, Tutor, Klausurentrainer, Skriptor | Throughput-oriented research, teaching, artifact creation |

**No Haiku.** Every role requires either strategic judgment or substantive production.

---

## 5. Coordination: JURA_STATE.md

Git captures file state but not learning state. `JURA_STATE.md` tracks current semester, active courses, Klausurtermine, Lernstand per Fach, Fristen, produced materials, and open questions.

### What it contains
- **Aktuelles Semester** — semester, Fachsemester, Studienziel
- **Kurse** — active courses mit Dozent, Status, Ziel, Klausurtermin
- **Lernstand** — per Fach: aktuelle Themen, erledigte Themen, offene Fragen
- **Fristen** — Klausurtermine, Anmeldefristen, Hausarbeit-Deadlines
- **Materialien-Index** — paths zu produced artifacts in `faecher/`
- **Last Agent Action** — was passiert, von wem, auf welchem Branch

### Rules
1. Orchestrator updates JURA_STATE.md after every significant action
2. Every agent session loads JURA_STATE.md at startup
3. Open questions feed back into Tutor and Professor decisions
4. Keep it under ~500 tokens — archive completed topics

### Notion Sync
Rico's Notion-Dashboard is the canonical planning hub. When Orchestrator modifies Stundenplan, Klausurtermine, or Lernstrategie, it must also update the corresponding Notion page via `mcp__claude_ai_Notion__notion-*` tools.

---

## 6. Git Workflow

Content in `faecher/` is gitignored (personal study material stays local per Rico's policy). Git tracks: `JURA_STATE.md`, `.claude/CLAUDE.md`, `.claude/agents/*.md`, and `docs/`.

### Branch strategy (for tracked files)
```
main
├── orchestrator/update-state-bgb-at-woche-2
├── methodiker/gutachtenstil-korrektur-v2
└── ...
```

### Rules
1. Never work on `main` directly for tracked files
2. Commit messages explain WHY: `{role}: {description}`
3. No squash — history shows learning evolution
4. For pure `faecher/` production (gitignored): no branch overhead needed

---

## 7. Extensibility

Adding a new Rechtsgebiet-Spezialist (e.g., Strafrechtler when StGB starts):

1. Create `jura-team/agents/strafrechtler.md` with YAML frontmatter
2. Add row to `jura-team/CLAUDE.md` role table
3. Add routing rules to `jura-team/ORCHESTRATOR.md`
4. Fill Dogmatik-Fokus (StGB AT/BT Systematik, Aufbau-Schemata)
5. Copy to `ReWi/.claude/agents/strafrechtler.md`
6. Update `ReWi/JURA_STATE.md` with new course

The base team is intentionally minimal. Specialists grow with your Studienplan.

---

## 8. What This System Doesn't Have (and Why)

| Missing element | Why |
|---|---|
| **Hausarbeit-Writer** | Klausurentrainer covers — same Gutachten skillset, just different length/apparat |
| **Strafrechtler** | Extensible — add when StGB starts (2./3. Semester) |
| **Gesetzestext-Agent** | Each Rechtsgebiet-Spezialist handles Gesetzesauslegung in their area |
| **Lerncoach / Learning-Psychology-Agent** | Tutor + Orchestrator + Professor cover this without separate role |
| **Präsentator / Mündliche-Prüfungs-Trainer** | Not needed for Grundstudium — add later if relevant |
| **Karriereberater / Stipendien-Coach** | Out of scope |

---

## 9. Comparison: Jura Team vs. Study Team vs. Law Team

| Dimension | Jura Team | Study Team | Law Team |
|---|---|---|---|
| **Purpose** | Learn law (student perspective) | Learn any subject | Handle personal legal disputes |
| **Total roles** | 8 core + extensible specialists | 6 fixed | 6 fixed |
| **Domain depth** | Deep German Rechtsdogmatik | Generic | Deep personal law |
| **Output style** | Gutachten, Schemata, Karteikarten | Notes, flashcards, quizzes | Widersprüche, Mahnungen, Einsprüche |
| **Language** | German | English (default) | German |
| **Horizon** | 5-9 years | Any | Per case |
| **Coordination** | JURA_STATE.md | STUDY_STATE.md | LAW_STATE.md |
| **Key discipline** | Gutachtenstil | Flexible | Fristen |

---

## 10. Context Management

### Subagent return summaries
Each subagent returns 500-1500 tokens to Orchestrator: what was accomplished, key findings, open questions, what the next agent needs.

### JURA_STATE budget
<500 tokens. Orchestrator compacts when courses end or semester changes.

### Per-role context loading

| Role | Must load | On-demand |
|------|-----------|-----------|
| Professor | JURA_STATE, task | existing cross-cutting notes in `_uebergreifend/systematik/` |
| Methodiker | JURA_STATE, task | `_uebergreifend/methodik/`, Rechtstheorie-Materialien |
| Rechtsgebiet-Spezialist | JURA_STATE, task, relevant Fach-Material | Gesetzestexte, Rechtsprechung, rep-Skripte |
| Bibliothekar | JURA_STATE, task | `rep/hofmann/`, `rep/openrewi/`, WebSearch |
| Tutor | JURA_STATE, task, relevant Vorlesungs-/Skript-Material | bestehende Notizen |
| Klausurentrainer | JURA_STATE, task, Fall-Sachverhalt | `faecher/{fach}/skripte/`, `faelle/` |
| Skriptor | JURA_STATE, task, source material (Vorlesung/Skript) | bestehende Schemata (avoid duplication) |
| Prüfer | JURA_STATE, Rico's Lösung, source material | Musterlösung falls vorhanden |
