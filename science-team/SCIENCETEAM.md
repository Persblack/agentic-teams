# AI Science Team — Academic Research Organization Blueprint

> **Purpose:** Document how an AI agentic swarm replicates a full academic research team — from literature discovery through model development, writing, review, and journal submission. This is the architecture behind the Paper 1 PhD dissertation system.

---

## 1. Design Principles

### Why replicate an academic team?

A PhD paper isn't written by one person doing one thing. It requires fundamentally different cognitive modes: creative theorizing, rigorous proof-checking, persuasive writing, adversarial critique, strategic positioning, and meticulous formatting. Humans context-switch between these modes poorly. Agents can specialize.

The academic system is modeled after how a well-resourced research group actually functions: a supervising professor sets direction, researchers develop theory, analysts run computations, a librarian manages the literature, writers produce prose, reviewers ensure quality, and an editor polishes for submission. Each role has distinct expertise, tools, and decision authority.

### Key design choices

| Choice | Rationale |
|---|---|
| **10 roles, flat hierarchy** | Academic work is more sequential than corporate work — a flat structure with one Orchestrator is sufficient |
| **No agile/sprint layer** | Paper writing doesn't have 2-week delivery cycles; work is organized by deliverables (sections, proofs, figures) not time-boxes |
| **Role = cognitive mode** | Each role represents a way of thinking, not a job title. The Reviewer thinks like a hostile referee; the Writer thinks like a communicator. Same material, different lenses |
| **Git-based coordination** | Every change is traceable, reviewable, and reversible. Branch-per-task with PR-based merging provides the quality gate |
| **Competitive execution (deferred)** | When two theoretical approaches seem viable, spawn both and let the Professor pick the winner. Deferred for Paper 1 — the theoretical approach is constrained by supervisor direction and prior work. Revisit for Paper 2+ if genuine uncertainty exists. |

---

## 2. The Ten Roles

### Layer 0: Coordination

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Orchestrator** | `ORCHESTRATOR.md` | Task decomposition, subagent spawning, git workflow, conflict resolution, dependency management | Workflow and process decisions. Never content decisions. |

The Orchestrator is a coordinator, not a doer. If it catches itself writing LaTeX, deriving equations, or editing code — it stops and spawns the right specialist. It thinks in workflows, dependencies, and parallel execution.

**Key capabilities:**
- Decomposes high-level tasks into role-specific subtasks with dependency graphs
- Manages git branching strategy (`{role}/{task-description}`)
- Spawns parallel or competitive subagents
- Assigns reviewers based on change type (model changes get Red Team; prose gets Reviewer; scope changes get Professor)
- Resolves conflicts: technical (Professor decides), quality (Reviewer decides), merge (Orchestrator resolves mechanically), scope (Orchestrator rejects and re-scopes)

### Layer 1: Strategic Direction

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Professor** | `PROFESSOR.md` | Strategic advice, scope control, publication strategy, PhD arc planning | What's in/out of scope. Which journal. What to work on next. |

The Professor is the strategic brain. It thinks like a senior faculty member who has supervised dozens of dissertations and reviewed for top journals. Its job is to ensure the paper is positioned correctly and resources are allocated to the highest-leverage work.

**Key capabilities:**
- Assesses paper progress and identifies the single highest-leverage improvement
- Controls scope ruthlessly — rejects tangents that don't serve Paper 1's core contribution
- Advises on the three-paper dissertation arc (theory → empirics → NLP)
- Thinks like "Reviewer 2" proactively — identifies what will be attacked
- Breaks ties between competing approaches based on strategic fit
- Direct and unsentimental: "The empirical section has no identification strategy" not "shows promise"

### Layer 2: Research & Analysis

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Researcher** | `RESEARCH.md` | Model development, proofs, literature gaps, empirical design | Technical correctness. What the math says. |
| **Analyst** | `ANALYST.md` | Python code, numerical experiments, sensitivity analysis, publication-quality figures | Computational results. What the numbers say. |
| **Librarian** | `LIBRARIAN.md` | Literature search, citation verification, BibTeX hygiene, Zotero management | What the literature says (and doesn't say). |

These three roles produce the raw material of the paper.

**Researcher** thinks like a scientist — skeptical, precise, evidence-driven. Every statement must be verifiable: proven from the model, supported by a cited source, or explicitly flagged as conjecture. It knows the intellectual lineage deeply (Husted 2005 → Cassimon et al. 2016 → Chen, Gerick & Tang 2023 → our paper) and can articulate precisely what each predecessor does and doesn't do.

**Analyst** thinks in code, data, and visualizations. All new computation is Python 3 (numpy, scipy, matplotlib, sympy). It translates `sections/model.tex` into working code, validates analytical solutions against numerical output, runs comparative statics, and produces figures at 300+ DPI for journal submission. Every script is reproducible, commented with equation references, and sanity-checked against known cases (e.g., lambda=0 recovers the standard Merton model).

**Librarian** is meticulous about sources. It searches Zotero first (via MCP tools), cross-references with BibTeX files, reads actual PDFs in `Literature/`, and never confuses what a paper says with what someone claims it says. It produces structured literature reports distinguishing between papers that are cited, available but uncited, and missing entirely.

### Layer 3: Writing & Communication

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Writer** | `WRITER.md` | LaTeX drafting, argument structure, revision, prose polishing | How to say it. Never what to say. |
| **Presenter** | `PRESENTER.md` | Conference talks, seminar slides, elevator pitches, Q&A preparation | How to present it. |

**Writer** produces publication-ready LaTeX matching the paper's established style, notation (`\P`, `\Q`, `\E`, `\N`, `\F`, `\diff`), and citation conventions (natbib: `\citet{}` for textual, `\citep{}` for parenthetical). Principles: concise over verbose, precise over impressive, active voice, one idea per paragraph. It preserves supervisor comments (`\ancomment{}`) and addresses them rather than deleting silently.

**Presenter** distills the paper into audience-appropriate formats. Conference talk (15-20 min, 12-15 slides), seminar (45-60 min, 25-30 slides), dissertation defense (full three-paper arc), or elevator pitch (1 minute). Core principle: figures over equations, one idea per slide, title every slide with the takeaway not the topic. Prepares answers to the five hardest Q&A questions.

### Layer 4: Quality Assurance

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Reviewer** | `REVIEW.md` | Scientific rigor assessment, fact-checking, citation verification, publishability judgment | Whether quality is sufficient. Accept/revise/reject verdict. |
| **Red Team** | `REDTEAM.md` | Adversarial stress-testing, counterexamples, edge cases, breaking assumptions | Whether the argument survives attack. Severity classification. |

**Reviewer** is "Reviewer 2" — the demanding referee at a top journal. It checks scientific validity (do results follow from assumptions?), mathematical correctness (step-by-step derivation checks), citation integrity (does the cited paper actually say what we claim?), clarity, and contribution positioning. Produces structured reviews with verdict, major issues, minor issues, citation checks, and line-by-line comments.

**Red Team** actively tries to break the argument. It enumerates assumptions (explicit and implicit), ranks them by fragility, constructs specific attacks (alternative functional forms, extreme parameter regimes, real-world scenarios), executes them, and classifies findings as fatal / significant / minor / survived. Specific attack vectors for this paper: functional form sensitivity, leverage extremes, default boundary behavior, constant Sharpe ratio assumption, recovery rate sensitivity, time horizon dependence, multi-period vs. single-period, and equity vs. total firm value optimization.

The key distinction: the Reviewer asks "is this paper good enough?" The Red Team asks "can I make this paper wrong?"

### Layer 5: Submission & Formatting

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Editor** | `EDITOR.md` | Submission formatting, reference list hygiene, cover letters, referee responses | Whether the manuscript is mechanically flawless. |

**Editor** is the last line of defense before submission. Runs a comprehensive pre-submission checklist: title page compliance, abstract word limit, clean LaTeX compilation, every `\cite` resolving, no orphan bibliography entries, consistent notation, all figures captioned and referenced, no leftover `\ancomment{}`/`\ricocomment{}` or TODOs, consistent English variant. Drafts cover letters and point-by-point referee responses. Knows journal-specific requirements for JBF, Review of Finance, and IME.

---

## 3. Orchestration Model

### Single flat Orchestrator

Unlike the tech company blueprint (which needs hierarchical orchestration), the academic system uses a single Orchestrator. This works because:

- **Lower parallelism** — most academic work is sequential: research → write → review
- **Fewer roles** — 10 vs. 25-30, manageable span of control
- **Single deliverable** — one paper, not a multi-service product
- **Clear dependency chains** — the model must exist before figures can be made, prose can be written, or reviews can be conducted

### Communication patterns

| Pattern | When | Example |
|---|---|---|
| **Directive** | Orchestrator → Role | "Researcher: extend model to include stochastic recovery rate" |
| **Escalation** | Role → Orchestrator → User | "Scope conflict: this change affects Paper 1's contribution framing" |
| **Lateral handoff** | Role → Role (via Orchestrator) | Researcher produces result → Writer drafts section → Reviewer reviews |
| **Competitive** | Role vs. Role → Professor | Two approaches compared; Professor picks winner |

### Escalation rules

**Subagents escalate to Orchestrator:**
- Blocked by missing information
- Decision outside their role's authority
- Discovered problem in another agent's work
- Unsure whether task is in scope

**Orchestrator escalates to user:**
- Strategic decisions affecting paper direction
- Competing approaches with no clear winner
- Resource-intensive work that might not be necessary
- Anything changing the paper's scope or contribution

---

## 4. Workflow Patterns

### Pattern 1: Linear Pipeline
```
Researcher → Writer → Reviewer → merge
```
Use when: a new theoretical result needs to be derived, written up, and reviewed.

### Pattern 2: Parallel Independent
```
Analyst: figures  |  Librarian: citations  |  Writer: prose
         └──────────── all merge ────────────┘
```
Use when: tasks don't depend on each other. Maximize throughput.

### Pattern 3: Competitive (deferred for Paper 1)
```
Researcher-A: approach 1  |  Researcher-B: approach 2
              └──── Professor: compare & pick ────┘
                              │
                         Winner merges
```
Use when: genuine uncertainty about the best theoretical approach. The overhead of two agents is justified when the decision matters.

**Paper 1 status:** Deferred. The theoretical approach is constrained by supervisor direction and prior work (Husted → Cassimon → Chen, Gerick & Tang → this paper). Competitive execution also doesn't exist natively in Claude Code — it would require manual parallel sessions or a wrapper script. Revisit for Paper 2+ if genuine uncertainty between approaches exists.

### Pattern 4: Build-Review Cycle
```
Agent works → Reviewer reviews → Agent revises → Reviewer re-reviews
```
Use when: quality matters and a single pass won't be enough. Cap at 2 review cycles — if not resolved, escalate to Professor.

### Pattern 5: Red Team Gauntlet
```
Researcher: new result → Red Team: attack → Researcher: defend/fix → Reviewer: final check
```
Use when: a result is central to the paper's contribution and must survive hostile scrutiny.

### Pattern 6: Full Section Build
```
[parallel] Researcher: technical content  |  Librarian: literature for section
     └──── Writer: draft section (uses both inputs) ────┘
                          │
              [parallel] Reviewer: review  |  Red Team: stress-test
                          │
                    Writer: revise
                          │
                    Reviewer: final check → merge
```
Use when: building a complete new section from scratch. The most resource-intensive pattern.

---

## 5. Git Workflow

### Branch strategy
```
main                                    # Stable, reviewed paper
│
├── researcher/justify-sharpe-ratio     # Role/task-description naming
├── analyst/payoff-curves
├── writer/numerical-section-draft
├── librarian/citation-audit-q1
│
├── researcher/recovery-model-concave   # Competitive branch A
├── researcher/recovery-model-stochastic # Competitive branch B
│
└── integration/numerical-complete      # Multi-role integration branch
```

### Rules
1. Never work on `main` directly — always branch
2. Never merge without review — even "obvious" changes
3. Never squash commits — granular history is valuable
4. Every commit message explains WHY, not WHAT: `{role}: {reasoning}`
5. Every merge to `main` goes through a PR with structured description
6. Prefer small, frequent PRs over large, rare ones

### Review assignments by change type

| Change type | Required reviewer | Optional reviewer |
|---|---|---|
| Model/proofs | Reviewer + Red Team | Professor (if scope change) |
| Numerical results | Reviewer + Analyst (cross-check) | Red Team (edge cases) |
| Prose/sections | Reviewer | Professor (if positioning) |
| Citations/bibliography | Reviewer + Librarian (cross-check) | — |
| Figures/tables | Reviewer | Analyst (methodology) |
| Scope changes | Professor | — |
| Strategic decisions | Professor | — |

---

## 6. Coordination: PAPER_STATE.md

Git captures code state but not decision state. Every new agent session starts with a fresh context window and must load role instructions, current paper state, and relevant literature. A compact `PAPER_STATE.md` file is the primary coordination mechanism — more important than git branching for practical agent coordination.

### What it contains

```markdown
# Paper State

## Current Phase
Phase 2: Model Development

## Section Status
| Section | Status | Last Agent | Last Action |
|---------|--------|------------|-------------|
| model.tex | In progress | Researcher | Extended to include default boundary |
| introduction.tex | Not started | — | — |
| numerical.tex | Blocked | Analyst | Waiting on model.tex completion |

## Open Decisions
- [ ] Include stochastic recovery rate? (Professor to decide)
- [ ] Target journal: JBF or Review of Finance? (Awaiting supervisor input)

## Agreed Notation
- lambda: CSR fraction of assets
- A_0: initial assets, L_0: liabilities
- theta: recovery rate
- Custom commands: \P, \Q, \E, \N, \F, \diff

## Last Agent Action
Researcher extended model to include first-passage-time default on branch researcher/default-mechanism. PR #4 open for review.

## Human Checkpoint Status
- Phase 1 checkpoint: PASSED (2024-01-15)
- Phase 2 checkpoint: PENDING
```

### Rules
1. The Orchestrator updates `PAPER_STATE.md` after every significant agent action
2. Every agent session loads `PAPER_STATE.md` as part of its context
3. Human checkpoints are recorded here with date and outcome
4. Open decisions are tracked here, not in git issues or comments

---

## 7. Citation Infrastructure

### The Problem
Two unsynchronized citation sources — the Zotero library and the `Literature/` folder — will cause the Librarian to silently produce incomplete literature reviews if both exist without reconciliation.

### The Fix

**Zotero is the canonical index.** `Literature/` is the content store.

1. **One-time reconciliation:** Diff the Zotero library against `Literature/` files. Import any papers that exist in `Literature/` but not Zotero. After this, Zotero is the single source of truth for what papers we have.

2. **Three-system architecture:**
   - **Zotero MCP** → search and metadata (titles, authors, abstracts, tags, collections)
   - **Literature/ PDFs** → content access by filename (Zotero MCP exposes metadata only, not PDF content)
   - **Literature/summaries/** → persistent per-paper markdown cache. Agents write a summary once after reading a paper; subsequent agents read the summary instead of re-processing the PDF. Format: `Literature/summaries/{AuthorYear}.md`

3. **Librarian workflow after reconciliation:**
   - Search Zotero for relevant papers (metadata + collection browsing)
   - For content: check `Literature/summaries/` first, then read the PDF from `Literature/` if no summary exists
   - Write a summary to `Literature/summaries/` after reading any PDF
   - Produce .bib entries from Zotero metadata, cross-referenced against existing `bibs/` files

---

## 8. The Paper Lifecycle

### Phase 0: Pipeline Validation
```
Librarian: one research question → Zotero search → one clean .bib entry → LaTeX compiles
Output: Verified end-to-end citation pipeline
```
**Why this comes first:** The entire system depends on the Librarian reliably going from a research question to a correctly formatted .bib entry that compiles in LaTeX. If the Zotero MCP → BibTeX pipeline doesn't work, nothing downstream works. One task, one question, one .bib entry.

### Phase 1: Foundation
```
Professor: assess current state, set priorities
Librarian: literature audit, gap analysis
Researcher: verify existing model, identify extensions needed
Output: Strategic direction, literature map, research agenda
```

**⏸ HUMAN CHECKPOINT:** Orchestrator presents the strategic direction, literature map, and research agenda to the user/supervisor for sign-off before proceeding. External feedback enters here — supervisor expectations, journal preferences, scope corrections.

### Phase 2: Model Development
```
Researcher: develop/extend theoretical model
Red Team: stress-test assumptions and results
Analyst: numerical verification of analytical solutions
Output: Verified propositions with proofs, numerical validation
```

**Presenter clarity check:** Before writing begins, the Presenter attempts to distill the model into a clear narrative: problem, gap, model, key result, implications — in three sentences. If this fails, the argument structure has problems that must be resolved before prose is written around it. This is cheaper than discovering structural issues in Phase 4.

**⏸ HUMAN CHECKPOINT:** Orchestrator presents the verified model, key results, and Presenter's distillation to the user/supervisor. This is the last opportunity to redirect before sections are written. External feedback on positioning, emphasis, and scope enters here.

### Phase 3: Section Writing
```
[For each section, Pattern 6: Full Section Build]
Researcher + Librarian → Writer → Reviewer + Red Team → Writer (revise)
Output: Complete drafted sections
```

**⏸ HUMAN CHECKPOINT:** Orchestrator presents the complete draft to the user/supervisor. Supervisor reads the actual prose and provides feedback before integration and polish. Misalignments caught here avoid expensive rework in Phases 4-5.

### Phase 4: Integration & Polish
```
Writer: ensure cross-section coherence, argument flow
Reviewer: full-paper review (not just section-level)
Professor: verify contribution statement and positioning
Output: Complete manuscript draft
```

### Phase 5: Pre-Submission
```
Editor: formatting compliance, reference hygiene, checklist
Presenter: prepare conference talk / elevator pitch (tests clarity)
Red Team: final adversarial pass
Output: Submission-ready manuscript, cover letter, presentation materials
```

### Phase 6: Submission & Response
```
Editor: submit to journal, track status
[After referee reports]
Professor: strategic response plan (what to concede, what to defend)
Researcher/Writer: implement revisions
Editor: point-by-point response letter
Output: Revised manuscript, response to referees
```

### Phase 7: Dissemination
```
Presenter: conference talks, seminar presentations
Librarian: update citations in Papers 2-3, maintain reference network
Professor: plan next steps for the dissertation arc
Output: Presentations, updated research agenda for Paper 2
```

---

## 9. What This System Doesn't Have (and Why)

| Missing element | Why it's absent | When it would be needed |
|---|---|---|
| **Agile/Sprint process** | Paper writing is deliverable-driven, not time-boxed. "Finish the proof" doesn't fit a 2-week sprint — it takes as long as the math requires. | If managing multiple papers simultaneously with hard deadlines |
| **Project Manager** | Single paper with one Orchestrator is sufficient span of control. No cross-team dependencies to manage. | Multi-paper dissertation with concurrent workstreams |
| **DevOps / CI/CD** | "Deployment" is a LaTeX compile + journal submission. No infrastructure to maintain. | If computational models needed cloud compute or reproducible environments |
| **Designer** | No UI. Figures are produced by the Analyst, formatted by the Editor. | If producing interactive visualizations or web-based supplements |
| **Support / Maintenance** | A published paper doesn't need ongoing maintenance. Errata are rare. | If maintaining a software package or dataset alongside the paper |
| **Growth / Marketing** | Academic dissemination (conferences, seminars) is handled by the Presenter. No paid acquisition or SEO. | If the research has a public-facing component (policy brief, media engagement) |
| **Subroles** | 10 roles already cover all required cognitive modes for a single paper. Further splitting would add coordination cost without proportional benefit. | If the paper required deep specialization in multiple subfields (e.g., both NLP and econometrics) |

---

## 10. Comparison: Science Team vs. Tech Company

| Dimension | Science Team | Tech Company |
|---|---|---|
| **Total roles** | 10 (fixed) | 8–30 (scales with complexity) |
| **Hierarchy** | Flat (1 Orchestrator) | Hierarchical (3-4 orchestration layers) |
| **Process model** | Deliverable-driven | Sprint-based agile |
| **Parallelism** | Low-moderate (mostly sequential) | High (many concurrent workstreams) |
| **Primary output** | One document (paper) | One product (app/service) |
| **Quality gates** | PR-based peer review + human checkpoints between phases | PR review + QA + security audit |
| **Competitive execution** | Deferred for Paper 1; available for Paper 2+ | Rare (A/B testing is post-deploy, not pre-build) |
| **"Deployment"** | LaTeX compile → journal submission | CI/CD → staged rollout → monitoring |
| **Post-release** | Referee responses, conference talks | Continuous: support, monitoring, growth, iteration |
| **Feedback loops** | Reviewer ↔ Writer (2 cycles max) | Multiple concurrent: QA ↔ Eng, Designer ↔ Frontend, Users ↔ Product |
| **Stakeholder communication** | Supervisor comments in LaTeX | Product briefs, sprint reviews, stakeholder demos |
| **Red Team** | Formal adversarial role attacking assumptions | Security audits + penetration testing |
| **Strategic oversight** | Professor (scope, positioning) | CEO + CPO + CTO (vision, product, technology) |
| **Literature equivalent** | Librarian manages citations and prior work | No direct equivalent (closest: competitive analysis) |

---

## 11. Context Management

Subagents operate within finite context windows. Without active management, agents lose coherence on long tasks and the Orchestrator's context fills with verbose subagent output.

### Subagent return summaries

When a subagent completes its task, it returns a summary to the Orchestrator — not its full output. Target 1,000-2,000 tokens covering:
- What was accomplished (deliverables produced, commits made)
- Key decisions made and why
- Blockers or escalations
- What the next agent in the pipeline needs to know

The Orchestrator should instruct subagents to end with a structured summary in their spawning template.

### PAPER_STATE.md budget

Keep `PAPER_STATE.md` under 500 tokens. The Orchestrator is responsible for compacting it after each phase — archiving resolved decisions, collapsing completed section entries, and keeping only the currently actionable state. A bloated `PAPER_STATE.md` wastes context in every agent session.

### Compaction strategy

When a task requires more work than fits in one context window:
1. The agent commits its in-progress work
2. Updates `PAPER_STATE.md` with what was done and what remains
3. The Orchestrator spawns a fresh agent to continue from where the previous one stopped

This is cheaper than trying to compress a single long session.

### Per-role context loading

Not every agent needs to read every file. Load only what's necessary.

| Role | Must load | On-demand |
|------|-----------|-----------|
| Researcher | PAPER_STATE.md, model.tex, assigned task files | Literature/summaries/, bibs/ |
| Writer | PAPER_STATE.md, section being written, model.tex | bibs/, notation reference |
| Reviewer | PAPER_STATE.md, section under review, model.tex | bibs/, Literature/summaries/ |
| Analyst | PAPER_STATE.md, model.tex, existing code in Codes/ | Section drafts (for figure placement) |
| Librarian | PAPER_STATE.md, bibs/, Zotero/ZOTERO.md | Literature/summaries/, section drafts |
| Professor | PAPER_STATE.md | Section drafts, model.tex (as needed) |
| Red Team | PAPER_STATE.md, model.tex, propositions/proofs | Analyst code (for numerical attacks) |
| Editor | PAPER_STATE.md, full manuscript, bibs/ | Journal guidelines |
| Presenter | PAPER_STATE.md, key results, figures | Full sections (for slide content) |

---

## 12. Evaluation Patterns

### Rubric scoring

Use structured rubrics to evaluate paper quality at phase checkpoints. Each dimension is scored 0.0-1.0:

| Dimension | What it measures | Scored by |
|-----------|-----------------|-----------|
| **Mathematical correctness** | Proofs valid, derivations error-free, boundary conditions correct | Reviewer + Red Team |
| **Contribution clarity** | Can the novelty be stated in one sentence? Is it genuinely new? | Professor + Presenter |
| **Literature coverage** | Key predecessors cited, positioned fairly, gaps identified | Librarian + Reviewer |
| **Argument coherence** | Logical flow from motivation → model → results → implications | Reviewer |
| **Robustness** | Results survive parameter perturbation, functional form changes, edge cases | Red Team + Analyst |
| **Presentation quality** | Writing clarity, figure quality, notation consistency | Writer + Editor |

### Decision thresholds

| Aggregate score | Action |
|----------------|--------|
| >= 0.8 | Ready to submit (proceed to next phase or submission) |
| 0.6 - 0.8 | Targeted revision — identify the lowest-scoring dimensions and assign specific agents to address them |
| < 0.6 | Rework required — escalate to Professor for strategic reassessment before continuing |

### End-state evaluation

Judge outputs, not process. A section that took three revision cycles but scores 0.9 is better than a section that passed review on the first try but scores 0.6. The Orchestrator should not penalize agents for needing iteration — iteration is the mechanism by which quality improves.

### LLM-as-judge for comparing approaches

When competitive execution produces two alternatives (deferred for Paper 1, available for Paper 2+):
1. Present both outputs to the Professor with the rubric
2. Score each independently on the relevant dimensions
3. Pick the higher-scoring approach
4. Document the comparison in the winning PR's description

This replaces subjective "which feels better" with structured comparison.

---

## 13. Lessons Transferable Between Systems

### From academia to tech
- **Competitive execution** — spawning two approaches and comparing is underused in engineering. Could apply to architecture decisions, UI designs, or algorithm selection.
- **Red Team as a dedicated role** — most tech teams treat security as an afterthought. A dedicated adversarial agent running continuously is more effective.
- **Structured review format** — the Reviewer's verdict/major/minor/line-by-line format is more rigorous than typical code review.

### From tech to academia
- **Sprint cadence** — even without formal sprints, periodic "what did we accomplish / what's blocked / what's next" checkpoints would improve academic workflow.
- **Hierarchical orchestration** — managing a multi-paper dissertation with a single flat Orchestrator will eventually hit limits. The tech company's layered approach (strategic → product → execution) could apply to a dissertation with strategic Orchestrator + per-paper Orchestrators.
- **DevOps thinking** — reproducible computational environments (containers, CI for LaTeX compilation, automated figure generation) would eliminate "works on my machine" problems in academic code.
- **Support/maintenance** — published papers increasingly come with code repositories and datasets. A maintenance role for these post-publication artifacts is becoming necessary.
