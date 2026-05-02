# Project State — Jura Study App

> **Diese Datei ist das primäre Koordinations-Dokument** für das studyapp-team. Jeder Agent liest sie bei Session-Start. Der Orchestrator updated sie nach jeder signifikanten Aktion.
>
> **Budget:** Aktiv-relevanter Teil ≤ 400 Token. Abgeschlossene Phasen werden archiviert (entweder unten in dieser Datei oder in `STUDYAPP_HISTORY.md` ausgelagert).

---

## Current Phase

**Phase:** 0 — Stack & Pipeline-Smoke
**Status:** active
**Started:** YYYY-MM-DD
**Goal:** Stack gewählt, ADR-001 geschrieben, End-to-End-Smoke-Test (1 Prompt → Modell-Call → deserialisiertes Output) läuft, Modell-Tier-Strategie Free/Pro dokumentiert.

### Austrittskriterien für Phase 0
- [ ] ADR-001 (Stack-Wahl) geschrieben und akzeptiert
- [ ] ADR-002 (Modell-Tier-Strategie Free/Pro) geschrieben und akzeptiert
- [ ] Smoke-Test: `prompts/_smoke-test.md` durch Pipeline → Schema-validiertes Output
- [ ] `evals/results/_smoke-test.json` enthält Resultat von 2 Goldstandard-Datenpunkten
- [ ] Repo-Struktur angelegt (`src/`, `prompts/`, `rubrics/`, `cases/`, `evals/{goldstandard,specs,results}/`, `taxonomy/`, `docs/adr/`)

---

## Tech-Track

### In Progress
| Story | Owner | Branch | Status |
|-------|-------|--------|--------|
| (noch keine — Phase 0 startet) | — | — | — |

### Blocked
- (keine)

### Open ADRs
- ADR-001: Stack-Wahl — Owner: Architect, Deadline: Phase 0 Ende
- ADR-002: Modell-Tier-Strategie Free/Pro — Owner: Architect + Prompt Engineer + Methodiker

### Deployment Status
- Production: nicht deployed (vor Phase 5)
- Staging: nicht deployed
- Local Dev: setup pending Phase 0

---

## Content-Track

### In Progress
| Story | Owner | Branch | Status |
|-------|-------|--------|--------|
| (noch keine — Phase 0 startet) | — | — | — |

### Prompt-Versionen (live)
- (keine — vor Phase 2)

### Eval-Coverage
| Prompt | Goldstandard-Datenpunkte | Coverage-Ziel | Status |
|--------|--------------------------|---------------|--------|
| (keine vor Phase 2) | — | ≥20 pro Prompt | — |

### Case-Library
| Rechtsgebiet | Cases | Ziel-MVP |
|--------------|-------|----------|
| BGB AT | 0 | 7 |
| Schuldrecht AT | 0 | 7 |
| Grundrechte | 0 | 6 |

### Themen-Taxonomie
- BGB AT: nicht geseedet
- Schuldrecht AT: nicht geseedet
- Grundrechte: nicht geseedet

### Tutor-Report-Backlog
- (leer — vor Beta)

### Drift-Alerts (letzte 7 Tage)
- (leer — vor Beta)

---

## MVP-Spec Implementation Status

Referenz: `docs/specs/2026-05-01-mvp-design.md`

- [ ] §3.1 Onboarding (Phase 1)
- [ ] §3.2 Fall wählen oder hochladen (Phase 1+2)
- [ ] §3.3 Gutachten einreichen (Phase 2)
- [ ] §3.4 Bewertung (Phase 2)
- [ ] §3.5 Cockpit (Phase 3)
- [ ] §3.6 Rückfragen (Phase 3)
- [ ] §3.7 Persistierung & Tracking (Phase 3)
- [ ] §3.8 Export (Phase 4)
- [ ] §4 Bewertungs-Modell (Phase 2)
- [ ] §5 Wissens-Map (Phase 3)
- [ ] §6 Datenmodell (Phase 1)
- [ ] §7 Pricing & Limits (Phase 4)
- [ ] §8 Quality, Trust & Tuning-Loop (Phase 5+6)
- [ ] §9 Onboarding-Profil (Phase 1)

---

## Rico-Prioritäten

> Rico editiert diese Liste selbst. Orchestrator folgt diesen Prioritäten bei Konflikten zwischen gleich-dringenden Tasks.

1. (placeholder — Rico fügt Prioritäten ein, sobald Phase 0 startet)

---

## Offene Entscheidungen

| Entscheidung | Owner | Deadline | Status |
|--------------|-------|----------|--------|
| Frontend-Framework (Next.js? Remix? Vite+React?) | Architect | Phase 0 | offen |
| Backend-Framework (Hono/Elysia/Fastify? Next.js Routes?) | Architect | Phase 0 | offen |
| Database (Postgres self-hosted? Supabase? Neon?) | Architect | Phase 0 | offen |
| Hosting (Vercel? Railway? Fly.io?) | Architect + DevOps | Phase 0 | offen |
| LLM-Provider primary (Anthropic? Multi-Provider?) | Architect + Prompt Engineer | Phase 0 | offen |
| Modell für Free-Tier vs Pro-Tier | Architect + Prompt Engineer + Methodiker | Phase 0 | offen |
| File-Storage für Foto-/PDF-Uploads (S3? R2? Supabase Storage?) | Architect + Backend | Phase 1 | offen |
| Payment-Provider (Stripe? Paddle? — DSGVO-Aspekt) | Architect + Security | Phase 4 | offen |

---

## Cross-Team-Vorschläge (offen)

| Vorschlag | Von | An | Status |
|-----------|-----|-----|--------|
| (keine offen) | — | — | — |

---

## Letzte Agent-Aktionen

| Datum | Agent | Branch | Aktion |
|-------|-------|--------|--------|
| YYYY-MM-DD | Orchestrator | — | Initial-State seeded, Phase 0 gestartet |

---

## Archive

> Abgeschlossene Phasen werden hierher verschoben oder in `STUDYAPP_HISTORY.md` ausgelagert.

(noch leer)
