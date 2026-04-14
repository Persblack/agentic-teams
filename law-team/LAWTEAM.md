# AI Law Team — German Personal Law Blueprint

> **Purpose:** Model a personal law team as specialized AI agents that help navigate German personal law across four core Rechtsgebiete: Vertragsrecht/Verbraucherrecht, Arbeitsrecht, Sozialrecht, and Verwaltungsrecht/Öffentliches Recht. Designed as a lean, extensible base that grows specialist by specialist as cases arise.

---

## 1. Design Principles

### Why a law team?

Legal questions require fundamentally different cognitive modes: understanding what the law says (legal analysis), assessing a specific situation (subsumption), deciding what to do (strategy), and producing the document (drafting). Doing all of these in one session produces shallow, unreliable results.

The law team separates these modes. Advisory specialists reason deeply about legal positions. The Legal Drafter converts that reasoning into documents. The Orchestrator manages Fristen — the single most critical function, since a missed deadline can destroy an otherwise strong legal position.

### Key design choices

| Choice | Rationale |
|---|---|
| **Standalone team** | Separate from tax-team. Tax-team lawyers cover business/tax-adjacent law; this team covers personal life law. |
| **Advisory roles are read-only** | All 4 specialists have no Edit/Write access. They produce assessments; the Drafter acts on them. |
| **Drafter never acts alone** | Always works from a specialist brief or explicit user instruction. Produces documents, not legal opinions. |
| **Flexible routing** | Requests route ad-hoc by Rechtsgebiet — not phase-based. |
| **Frist tracking as first-class concern** | German law is deadline-driven. LAW_STATE.md tracks all open Fristen. Orchestrator flags overdue items at startup. |
| **Extensible by design** | Base team covers 4 areas. New specialists (Mietrecht, Familienrecht, Erbrecht) are added by creating one agent file and updating CLAUDE.md + ORCHESTRATOR.md. |
| **German output by default** | All agent output in German. Fachbegriffe stay in German. Documents use formal Schriftsprache (Sie-Form). |

---

## 2. The Six Roles

### Layer 0: Coordination

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Orchestrator** | `ORCHESTRATOR.md` | Request routing, Frist tracking, LAW_STATE.md management | Workflow and process decisions. Never substantive legal decisions. |

### Layer 1: Advisory Specialists

| Role | File | Model | Core function | Decision authority |
|---|---|---|---|---|
| **Vertragsrecht & Verbraucherrecht** | `agents/vertragsrecht.md` | Sonnet | AGB, Kaufrecht, Widerruf, Gewährleistung, Mahnverfahren | Contract interpretation and consumer rights assessment |
| **Arbeitsrecht** | `agents/arbeitsrecht.md` | Opus | Kündigung, Abmahnung, Aufhebungsvertrag, Scheinselbständigkeit | Employment law assessment and strategy |
| **Sozialrecht** | `agents/sozialrecht.md` | Sonnet | GKV/PKV, ALG, Bürgergeld, Rente, SGB-Widersprüche | Social law assessment and Widerspruch strategy |
| **Verwaltungsrecht & ÖR** | `agents/verwaltungsrecht.md` | Opus | Bescheide, Widersprüche, OWiG, Baurecht, Ausländerrecht | Administrative law assessment and challenge strategy |

### Layer 2: Production

| Role | File | Model | Core function | Decision authority |
|---|---|---|---|---|
| **Legal Drafter** | `agents/drafter.md` | Sonnet | Widersprüche, Mahnungen, Kündigungen, Beschwerden, Einsprüche | Document formatting and structure. Never legal substance. |

---

## 3. Orchestration Model

### Single flat Orchestrator

The law team uses a single Orchestrator — requests are personal, sequential, and handled by one person. The variety of Rechtsgebiete adds breadth, but the request flow is linear enough that a flat model works.

### Flexible routing

| Request type | Routing |
|---|---|
| "Ist diese AGB-Klausel wirksam?" | Vertragsrecht Specialist |
| "Mein Arbeitgeber hat mir eine Abmahnung geschickt" | Arbeitsrecht Specialist |
| "Krankenkasse hat meinen Antrag abgelehnt" | Sozialrecht Specialist → Drafter |
| "Ich habe einen Bußgeldbescheid bekommen" | Verwaltungsrecht Specialist → Drafter |
| "Schreib mir eine Kündigung für meinen Handyvertrag" | Drafter directly |
| "Kann ich meine fristlose Kündigung anfechten?" | Arbeitsrecht Specialist → Drafter |
| "Was für Fristen habe ich?" | Orchestrator answers directly from LAW_STATE.md |

---

## 4. Model Tier Allocation

| Tier | Model | Roles | Rationale |
|---|---|---|---|
| **Critical judgment** | Opus | Arbeitsrecht, Verwaltungsrecht & ÖR | High-stakes, procedurally complex, deadline-sensitive advisory with significant consequences |
| **Production advisory** | Sonnet | Vertragsrecht/Verbraucherrecht, Sozialrecht | More formulaic areas with established frameworks and procedures |
| **Document production** | Sonnet | Legal Drafter | Quality writing execution, not deep legal reasoning |

---

## 5. Coordination: LAW_STATE.md

LAW_STATE.md tracks active cases, document queue, key personal facts, open Fristen, and last agent action. Every agent reads it on startup.

### Rules
1. Orchestrator updates LAW_STATE.md after every significant agent action
2. Every agent session loads LAW_STATE.md as part of its context
3. All open Fristen must be listed with exact dates (YYYY-MM-DD) — Orchestrator flags items due within 14 days
4. Resolved cases get a one-line summary and archive
5. Keep it compact — archive closed cases aggressively

---

## 6. Extensibility

Adding a new specialist (e.g., Mietrecht, Familienrecht, Erbrecht, Strafrecht):

1. Create `law-team/agents/{area}.md` with standard YAML frontmatter
2. Add the role to `CLAUDE.md` role table
3. Add routing rule to `ORCHESTRATOR.md`
4. No other changes needed

The base team is intentionally minimal. Specialists are added per-case, not upfront.

---

## 7. What This System Doesn't Have (and Why)

| Missing element | Why it's absent |
|---|---|
| **Mietrecht** | Very relevant but not in base — add as first extension when a housing case arises |
| **Familienrecht / Erbrecht** | Case-specific; extend when needed |
| **Strafrecht** | Requires a licensed Strafverteidiger — AI cannot substitute for criminal defense |
| **Gerichtsvertretung** | AI cannot represent in court. Agents prepare; licensed counsel represents. |
| **Business/corporate law** | Covered by tax-team Corporate Lawyer |
| **IT/Privacy/GDPR** | Covered by tax-team IT/Privacy Lawyer |
| **Steuerrecht** | Covered by tax-team (all 9 roles) |
