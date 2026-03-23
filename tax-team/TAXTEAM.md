# AI Tax Team — German Multi-Entity Tax & Legal Blueprint

> **Purpose:** Model a personal tax and legal advisory team as specialized AI agents that help a German entrepreneur manage taxes across 9 business entities, optimize structures, handle international questions, and maintain legal compliance for digital platforms.

---

## 1. Design Principles

### Why a tax team?

Tax management is not a single activity. It requires fundamentally different cognitive modes: strategic restructuring advice (how to minimize total tax burden), ongoing bookkeeping (categorizing and reconciling transactions), specialized filing knowledge (different rules for each tax type), international structuring (foreign entity design, transfer pricing), and legal compliance (GDPR, JMStV for adult platforms, hosting law). Trying to do all of these in one session produces shallow, error-prone results.

The tax team models how a well-resourced entrepreneur would actually operate: a tax optimizer evaluates structure, a bookkeeper maintains clean records, specialists handle each filing type, a lawyer handles legal risk, and a compliance reviewer ensures nothing falls through the cracks.

### Key design choices

| Choice | Rationale |
|---|---|
| **Flexible routing** | Tax requests are ad-hoc: "optimize my structure" is different from "file Q2 VAT" which is different from "what does JMStV require?" Each request routes to the right agents |
| **Advisory roles are read-only** | Tax Optimizer, International Tax Advisor, and both lawyers have no Edit/Write access — they produce advisory reports. Production roles (Bookkeeper, filing specialists) do the actual work |
| **German law as the base** | All roles assume German legal and tax framework. International advisor handles deviations. |
| **Entity-aware by default** | All agents know the 9-entity portfolio and reference TAX_STATE.md for current filing status |
| **Legal + tax integrated** | IT/Privacy Lawyer and Corporate Lawyer are first-class roles, not afterthoughts — for a digital adult platform, legal risk IS tax risk |

---

## 2. The Ten Roles

### Layer 0: Coordination

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Orchestrator** | `ORCHESTRATOR.md` | Task decomposition, agent routing, git workflow, TAX_STATE.md management | Workflow and process decisions. Never substantive tax or legal decisions. |

### Layer 1: Strategy

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Tax Optimizer** | `agents/tax-optimizer.md` | Domestic restructuring, GmbH/GbR optimization, salary vs dividend, loss utilization, Gewerbesteuerhebesatz | What structure minimizes domestic German tax burden |
| **International Tax Advisor** | `agents/international-tax-advisor.md` | Foreign entity structuring, IP holding, transfer pricing, CFC rules (§7–14 AStG), Wegzugsbesteuerung, double-tax treaties | What international structures are defensible and optimal |

### Layer 2: Bookkeeping

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Bookkeeper** | `agents/bookkeeper.md` | Transaction categorization, SKR03/SKR04, multi-entity accounting, intercompany billing, DATEV prep | How transactions are classified. Chart of accounts decisions. |

### Layer 3: Filing & Legal

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **VAT Specialist** | `agents/vat-specialist.md` | UStVA, UStJE, EU OSS, 7%/19% splits, Reverse Charge, international digital services VAT | VAT classification and filing for all entities |
| **Corporate Tax Specialist** | `agents/corporate-tax-specialist.md` | Körperschaftsteuer, Gewerbesteuer, Feststellungserklärungen, §8b KStG | Corporate-level tax filings per entity |
| **Personal Tax Specialist** | `agents/personal-tax-specialist.md` | Einkommensteuer, Anlage G/S/KAP, Günstigerprüfung, managing income across entities | Personal tax return optimization |
| **IT/Privacy Lawyer** | `agents/it-privacy-lawyer.md` | JMStV, DSGVO/GDPR, age verification, hosting contracts, data residency, FSM, App Store | Legal compliance for digital platforms. Whether something is permitted. |
| **Corporate Lawyer** | `agents/corporate-lawyer.md` | Foreign entity setup, intercompany agreements, Gesellschaftsverträge, licensing structures | Corporate law and transaction documentation |

### Layer 4: Quality Assurance

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Compliance Reviewer** | `agents/compliance-reviewer.md` | Betriebsprüfung readiness, cross-entity consistency, deadline tracking, Finanzamt correspondence | Whether the full picture is consistent and audit-ready |

---

## 3. Orchestration Model

### Single flat Orchestrator

The tax team uses a single Orchestrator for the same reason as the study team — requests are personal, sequential, and handled by one person (Rico). The entity count (9 businesses) adds complexity, but the request flow is still linear enough that a flat model works.

### Flexible routing (not phase-based)

| Request type | Routing |
|---|---|
| "Optimize my structure" | Tax Optimizer → International Tax Advisor → Compliance Reviewer |
| "File Q3 VAT for Holding" | VAT Specialist |
| "Prepare annual Körperschaftsteuer for Holding" | Corporate Tax Specialist |
| "What does JMStV require for Bordello?" | IT/Privacy Lawyer |
| "Should I set up a Cyprus company?" | International Tax Advisor → Corporate Lawyer |
| "Is Bordello's hosting setup GDPR-compliant?" | IT/Privacy Lawyer → Compliance Reviewer |
| "Reconcile Kleinstad Roastery accounts" | Bookkeeper |
| "Are we ready for a Betriebsprüfung?" | Compliance Reviewer → (escalates to advisors if issues found) |
| "What's my personal tax bill?" | Personal Tax Specialist |
| "What's due this month?" | Orchestrator answers directly from TAX_STATE.md |

---

## 4. Model Tier Allocation

| Tier | Model | Roles | Rationale |
|---|---|---|---|
| **Critical judgment** | Opus | Tax Optimizer, International Tax Advisor, IT/Privacy Lawyer, Corporate Lawyer, Compliance Reviewer | These roles make high-stakes advisory decisions with significant financial and legal consequences. Reasoning depth matters. |
| **Production work** | Sonnet | Bookkeeper, VAT Specialist, Corporate Tax Specialist, Personal Tax Specialist | These roles apply established procedures and produce filings, reconciliations, and reports. Sonnet balances quality with throughput. |

---

## 5. Coordination: TAX_STATE.md

TAX_STATE.md tracks the 9-entity filing status, upcoming deadlines, open decisions, and last agent action. Every agent reads it on startup.

### Rules
1. Orchestrator updates TAX_STATE.md after every significant agent action
2. Every agent session loads TAX_STATE.md as part of its context
3. Filing status updates after every completed filing
4. Open decisions are resolved by the appropriate advisory role and marked closed
5. Keep it under 600 tokens — compact when a full tax year closes
6. Cross-entity conflicts (e.g., a Bookkeeper classification that affects a VAT Specialist filing) are escalated to the Orchestrator for routing to the appropriate advisory role. ORCHESTRATOR.md owns the full conflict resolution logic.

---

## 6. Entity Portfolio

| # | Entity | Structure | Tax notes |
|---|--------|-----------|-----------|
| 00 | Einzelgewerbe / Unternehmensberatung | Sole proprietorship | Income flows directly to personal ESt |
| 01 | RK Holding GmbH | GmbH | §8b KStG dividend privilege; management fee income from subsidiaries |
| 02 | Frameway | GbR | EORI registered; imports involved; shared profits go to ESt |
| 03 | Kleinstad Roastery | Company (type TBD) | Packaged food goods: 7% VAT rate |
| 04 | RiCharge | GbR | EV charging; electricity as input cost |
| 05 | Snacks | Sole or GbR | Food business: 7% for food items; 19% for non-food |
| 06 | MilkyCream (Eisdiele) | Company (type TBD) | Ice cream: 7% in-shop, 19% to-go in some cases |
| 07 | Kaffee Klatsch / Klatte Catering | GbR | Catering: mixed rates depending on service type |
| 08 | Bordello | Sole or GbR (TBD) | Primary source of international revenue; JMStV applies; EU OSS required; IP structuring candidate |

---

## 7. What This System Doesn't Have (and Why)

| Missing element | Why it's absent |
|---|---|
| **Accountant (Steuerberater) role** | The agents ARE the advisory layer. A human Steuerberater would be the external checkpoint. |
| **Payroll specialist** | Not yet relevant — no employees mentioned. Add when needed. |
| **Customs/Trade specialist** | Frameway has EORI but customs complexity is low for now. Escalate to Corporate Lawyer if needed. |
| **Auditor role** | Compliance Reviewer covers Betriebsprüfung prep. External audit is a human function. |
