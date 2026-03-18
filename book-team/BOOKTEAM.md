# Book-Writing Team — Organization Blueprint

> **Purpose:** Model a book-writing team as specialized AI agents that produce non-fiction and technical books on any subject. This covers the full book pipeline — from thesis and structure through research, drafting, illustration, and editing.

---

## 1. Design Principles

### Why a book team?

Writing a book is not a single activity. It requires fundamentally different cognitive modes: strategic structuring (what the book argues and how it's organized), deep research (finding and synthesizing source material), disciplined drafting (turning outlines into prose), visual design (making complex ideas tangible through diagrams), and rigorous editing (catching errors and ensuring consistency). Trying to do all of these in one session leads to voice drift, structural incoherence, and factual gaps.

The book team models how a well-resourced author would actually operate: an author plans the structure and voice, a researcher gathers material, a writer drafts prose, an illustrator creates visuals, and an editor ensures quality. Each role has distinct expertise, tools, and decision authority.

### Key design choices

| Choice | Rationale |
|---|---|
| **6 roles, flat hierarchy** | Book writing is sequential and focused — a lean team with one Orchestrator keeps coordination overhead low |
| **Chapter-based pipeline** | Each chapter moves independently through outline → research → draft → illustrate → edit → revise → final. Multiple chapters can be at different stages simultaneously |
| **Role = cognitive mode** | Each role represents a way of engaging with the book, not a job title. The Author thinks strategically; the Editor thinks critically. Same material, different lenses |
| **Git-based coordination** | Every chapter version is traceable, versionable, and revisable. The revision history shows how the book evolved |
| **No Haiku tier** | Every role requires either strategic judgment or substantive production. There are no purely mechanical roles in book production |
| **Writer and Illustrator can't web search** | The Writer works from provided materials (outlines + research briefs), not from independent research. This ensures the Researcher is the single source of truth for factual content. The Illustrator works from chapter drafts, not independent reference |

---

## 2. The Six Roles

### Layer 0: Coordination

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Orchestrator** | `ORCHESTRATOR.md` | Task decomposition, agent routing, git workflow, BOOK_STATE.md management | Workflow and process decisions. Never content decisions. |

The Orchestrator routes book work to the right agents in the right order. It doesn't write, research, or edit — it figures out which agents should do those things and in what sequence.

### Layer 1: Strategy

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Author** | `agents/author.md` | Thesis, structure, voice, chapter outlines, conflict resolution | What the book says, how it's organized, what voice it uses. |

The Author thinks like an experienced book planner. It defines the book's thesis, designs the chapter structure, establishes the style guide, and resolves content conflicts. It produces outlines and structural guidance — not prose.

### Layer 2: Investigation

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Researcher** | `agents/researcher.md` | Source gathering (web + files), research briefs, citations, BibTeX | What the sources say. Factual accuracy of gathered information. |

The Researcher is the team's investigator. It finds, reads, and synthesizes information from the web, user-provided files, and any available sources. It produces structured research briefs that the Writer uses as input.

### Layer 3: Production

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Writer** | `agents/writer.md` | Chapter drafting, voice consistency, transitions, markdown + LaTeX output | How to say it. Prose quality and readability. |
| **Illustrator** | `agents/illustrator.md` | Diagrams, figures, visual concepts, captions, alt text | How to show it. Visual clarity and consistency. |

**Writer** transforms the Author's outlines and Researcher's briefs into polished prose. It maintains voice consistency across chapters and handles transitions, introductions, and conclusions.

**Illustrator** creates text-based diagrams and figures (Mermaid, TikZ, ASCII, SVG descriptions) that make complex ideas visually graspable. It maintains visual consistency across the book's figures.

Writer and Illustrator can work in parallel on the same chapter — the Writer handles text while the Illustrator handles visuals.

### Layer 4: Quality

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Editor** | `agents/editor.md` | Developmental editing, copy editing, fact-checking, consistency review | Whether the book is accurate, clear, and consistent. |

The Editor reviews chapters for developmental issues (does the argument work?), copy issues (is the prose clean?), factual accuracy (do claims match sources?), and cross-chapter consistency (does terminology match?). It produces edit reports with verdicts — not direct changes.

---

## 3. Orchestration Model

### Single flat Orchestrator

Like the science and study teams, the book team uses a single Orchestrator. This works because:

- **Sequential pipeline** — most book work follows a clear dependency chain: outline → research → draft → edit
- **Fewer roles** — 6 total, very manageable span of control
- **Single book** — one book project, not a multi-project organization
- **Clear dependencies** — you can't draft a chapter that hasn't been outlined and researched

### Flexible chapter-based routing

The Orchestrator routes work based on chapter readiness and user requests. Unlike the science team (paper lifecycle phases) or tech team (sprint cadence), the book team processes chapters through a pipeline where different chapters can be at different stages:

| Chapter stage | Next agent | Trigger |
|---|---|---|
| No outline | Author | User requests chapter or Orchestrator identifies gap |
| Outlined, not researched | Researcher | Outline approved |
| Researched, not drafted | Writer + Illustrator (parallel) | Research brief complete |
| Drafted, not edited | Editor | Draft complete |
| Edited with "Revise" verdict | Writer | Edit report received |
| All chapters edited | Editor (full review) | Manuscript assembly |

---

## 4. Model Tier Allocation

| Tier | Model | Roles | Rationale |
|---|---|---|---|
| **Critical judgment** | Opus | Author, Editor | These roles make evaluative decisions — what the book should say, whether the book is good enough — where reasoning depth matters most |
| **Production work** | Sonnet | Researcher, Writer, Illustrator | These roles produce content — research reports, chapter prose, diagrams. Sonnet balances quality with throughput |

**Why no Haiku tier:** Every book team role requires either strategic judgment or substantive content production. There are no formatting-only or checklist-only roles where speed matters more than depth.

---

## 5. Coordination: BOOK_STATE.md

Git captures file state but not book state. `BOOK_STATE.md` tracks the book's thesis, chapter progress, materials, and open decisions. Every agent reads it on startup.

### What it contains

- **Book-level** — title, thesis, target audience, voice/tone
- **Chapter Progress** — table with status per chapter (outline → research → draft → illustrate → edit → revise → final)
- **Materials** — paths to source files, reference PDFs, figure assets
- **Open Decisions** — items needing Author or user input
- **Last Agent Action** — what was done, by whom, on which branch

### Rules

1. The Orchestrator updates `BOOK_STATE.md` after every significant agent action
2. Every agent session loads `BOOK_STATE.md` as part of its context
3. Chapter status transitions are tracked — the Orchestrator knows which chapters are ready for the next pipeline stage
4. Keep it under 500 tokens — compact after major milestones

---

## 6. Git Workflow

### Branch strategy
```
main                                    # Stable, reviewed chapters
│
├── author/book-outline                 # Role/task-description
├── researcher/ch03-machine-learning
├── writer/ch03-machine-learning
├── illustrator/ch03-figures
│
└── integration/ch03-machine-learning   # Multi-role chapter assembly
```

### Rules

1. Never work on `main` directly — always branch
2. Commit messages explain WHY: `{role}: {description}`
3. Chapter drafts go through Editor review before merging to main
4. Outlines, research briefs, and figures can merge with lighter review
5. Never squash commits — the revision history shows how the book evolved

---

## 7. What This System Doesn't Have (and Why)

| Missing element | Why it's absent | When it would be needed |
|---|---|---|
| **Sprint cadence** | Book writing is chapter-pipelined, not time-boxed. "Write chapter 5" doesn't fit a 2-week sprint. | If writing to a publisher's deadline with milestone deliverables |
| **Red Team** | Books don't need adversarial stress-testing. The Editor covers accuracy and consistency. | If writing a book that will face hostile expert review (e.g., policy, controversial science) |
| **Presenter** | No presentation component. The book is the final artifact. | If the book includes a companion lecture series or conference talk |
| **Librarian** | No separate citation management role. The Researcher handles sources directly. | If the bibliography exceeds ~200 sources (use the science team's Librarian pattern) |
| **Copywriter** | No marketing copy needed during writing. The book's content speaks for itself. | If self-publishing and need jacket copy, Amazon descriptions, or marketing materials |
| **Multiple quality roles** | One Editor is sufficient for a single book. | If writing a multi-volume work or need separate developmental and copy editors |

---

## 8. Comparison: Book Team vs. Other Teams

| Dimension | Book Team | Study Team | Science Team | Tech Team |
|---|---|---|---|---|
| **Total roles** | 6 (fixed) | 6 (fixed) | 10 (fixed) | 8–30 (scales) |
| **Hierarchy** | Flat (1 Orchestrator) | Flat (1 Orchestrator) | Flat (1 Orchestrator) | Hierarchical |
| **Process model** | Chapter pipeline | Flexible routing | Deliverable-driven phases | Sprint-based agile |
| **Parallelism** | Moderate (Writer + Illustrator) | Low (mostly sequential) | Low-moderate | High |
| **Primary output** | One book (chapters + figures) | Study materials + knowledge | One document (paper) | One product (app/service) |
| **Quality gates** | Editor review + full-manuscript pass | Critic review | PR-based review + human checkpoints | PR review + QA + security |
| **Workflow trigger** | Chapter readiness | User request (ad-hoc) | Phase progression | Sprint backlog |
| **Web access** | Researcher + Author + Editor | All roles | Limited (Researcher, Librarian) | Limited (relevant roles) |
| **Model tiers** | 2 (Opus, Sonnet) | 2 (Opus, Sonnet) | 3 (Opus, Sonnet, Haiku) | 3 (Opus, Sonnet, Haiku) |
| **Stakeholder** | Author + readers | Single learner | Supervisor + journal reviewers | Users + business |

---

## 9. Context Management

### Subagent return summaries

When a subagent completes its task, it returns a summary to the Orchestrator — not its full output. Target 1,000–2,000 tokens covering:
- What was accomplished (chapters drafted, research completed, edit verdicts)
- Key decisions made
- Open questions or follow-up needs
- What the next agent in the pipeline needs to know

### BOOK_STATE.md budget

Keep `BOOK_STATE.md` under 500 tokens. The Orchestrator compacts it at major milestones — archiving completed chapters to a simple list, collapsing resolved decisions, keeping only currently actionable state.

### Per-role context loading

| Role | Must load | On-demand |
|------|-----------|-----------|
| Author | BOOK_STATE.md, assigned task | Existing chapter outlines, style guide |
| Researcher | BOOK_STATE.md, chapter outline, assigned task | Prior research on related topics |
| Writer | BOOK_STATE.md, chapter outline, research brief, style guide | Adjacent chapter drafts (for transitions) |
| Illustrator | BOOK_STATE.md, chapter draft, visual style guide | Existing figures (for consistency) |
| Editor | BOOK_STATE.md, chapter draft, research brief, style guide | Other chapter drafts (for cross-chapter review) |
