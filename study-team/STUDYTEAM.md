# AI Study Team — Lifelong Learning Organization Blueprint

> **Purpose:** Model a personal study team as specialized AI agents that help a post-university professional learn any subject, research topics, and produce study materials. This covers the full learning pipeline — from curriculum design through research, tutoring, content production, and quality review.

---

## 1. Design Principles

### Why a study team?

Learning is not a single activity. It requires fundamentally different cognitive modes: strategic sequencing (what to learn and in what order), deep investigation (finding and synthesizing information), interactive teaching (adapting explanations to the learner), material production (creating durable artifacts), and critical review (catching errors and filling gaps). Trying to do all of these in one session leads to shallow coverage and missed connections.

The study team models how a well-resourced learner would actually operate: a curriculum designer maps the territory, a researcher digs into sources, a tutor explains concepts interactively, a content creator produces study materials, and a critic ensures accuracy. Each role has distinct expertise, tools, and decision authority.

### Key design choices

| Choice | Rationale |
|---|---|
| **6 roles, flat hierarchy** | Study is more personal and sequential than corporate or academic work — a lean team with one Orchestrator keeps coordination overhead low |
| **No phase-based pipeline** | Unlike a paper (which moves through research → writing → review), study requests are ad-hoc and varied. A flexible routing model handles "teach me X" differently from "make me flashcards on Y" |
| **Role = cognitive mode** | Each role represents a way of engaging with material, not a job title. The Tutor thinks like a teacher; the Critic thinks like a fact-checker. Same material, different lenses |
| **Git-based coordination** | Every produced artifact is traceable, versionable, and revisable. Materials improve over time through iterative refinement |
| **No Haiku tier** | Study work requires either judgment (Opus) or substantive production (Sonnet). There are no purely mechanical roles where speed trumps depth |
| **Web access for all roles** | Unlike academic writing (where the Writer shouldn't web-search), every study role benefits from being able to look things up. Knowledge retrieval is core to learning |

---

## 2. The Six Roles

### Layer 0: Coordination

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Orchestrator** | `ORCHESTRATOR.md` | Task decomposition, agent routing, git workflow, STUDY_STATE.md management | Workflow and process decisions. Never content decisions. |

The Orchestrator routes study requests to the right agents in the right order. It doesn't teach, research, or create materials — it figures out which agents should do those things and in what sequence.

### Layer 1: Strategy

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Curriculum Designer** | `agents/curriculum-designer.md` | Learning paths, topic sequencing, depth calibration, prerequisite mapping | What to learn, in what order, at what depth. |

The Curriculum Designer thinks like an experienced educator planning a course. It assesses where the learner is, where they want to go, and designs the most efficient path between those points. It considers prerequisites, learning objectives, and appropriate depth for the learner's goals (career development vs. hobby vs. deep expertise).

### Layer 2: Investigation

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Researcher** | `agents/researcher.md` | Web + file research, source synthesis, topic deep-dives, reference gathering | What the sources say. Factual accuracy of gathered information. |

The Researcher is the team's investigator. It finds, reads, and synthesizes information from the web, files, and any available sources. It produces structured research reports that other agents (Tutor, Content Creator) use as input. It distinguishes between well-established facts, contested claims, and areas of active debate.

### Layer 3: Teaching & Production

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Tutor** | `agents/tutor.md` | Socratic dialogue, worked examples, adaptive explanations, session artifacts | How to explain it. Pacing and pedagogy. |
| **Content Creator** | `agents/content-creator.md` | Notes, flashcards, quizzes, study guides, summary reports | What format materials take. Production quality. |

**Tutor** thinks like a skilled private teacher. It uses Socratic questioning, worked examples, analogies, and progressive complexity to build understanding. It adapts to the learner's responses — if something isn't clicking, it tries a different angle. It produces session artifacts (key takeaways, practice problems) as durable records of what was covered.

**Content Creator** produces polished study materials in various formats. It takes research and learning objectives as input and creates notes, flashcards (question/answer pairs), quizzes (with answer keys), concept maps, summary reports, and study guides. It focuses on making materials that are useful for review and retention.

### Layer 4: Quality Assurance

| Role | File | Core function | Decision authority |
|---|---|---|---|
| **Critic** | `agents/critic.md` | Accuracy review, gap analysis, fact-checking, completeness assessment | Whether materials are correct and complete. |

The Critic reviews materials and research for accuracy, completeness, and clarity. It fact-checks claims, identifies gaps in coverage, flags unsupported assertions, and assesses whether materials achieve their learning objectives. It does not produce content — it evaluates it.

---

## 3. Orchestration Model

### Single flat Orchestrator

Like the science team, the study team uses a single Orchestrator. This works because:

- **Lower parallelism** — most study work is sequential: research → create materials → review
- **Fewer roles** — 6 total, very manageable span of control
- **Single learner** — one person's study needs, not a multi-person project
- **Clear dependency chains** — you can't create flashcards on a topic that hasn't been researched yet

### Flexible routing (not phase-based)

Unlike the science team (which follows a paper lifecycle) or the tech team (which uses sprint cadence), the study team uses **flexible routing**. The Orchestrator analyzes each request and routes to the appropriate agents:

| Request type | Routing |
|---|---|
| "I want to learn X" | Curriculum Designer → Researcher → Content Creator |
| "Teach me about Y" | Tutor (Researcher first if topic is new) |
| "Make me flashcards on Z" | Content Creator (Researcher first if topic is new) |
| "Write a deep-dive on W" | Researcher → Content Creator → Critic |
| "Quiz me on V" | Content Creator → Tutor |
| "What should I study next?" | Curriculum Designer |
| "Is this correct?" | Critic |

This flexibility matches the ad-hoc nature of personal study — the learner might want a tutoring session one day and flashcards the next.

---

## 4. Model Tier Allocation

| Tier | Model | Roles | Rationale |
|---|---|---|---|
| **Critical judgment** | Opus | Curriculum Designer, Critic | These roles make evaluative decisions — what's worth learning, whether materials are accurate — where reasoning depth matters most |
| **Production work** | Sonnet | Researcher, Tutor, Content Creator | These roles produce content — research reports, explanations, study materials. Sonnet balances quality with throughput |

**Why no Haiku tier:** Every study team role requires either strategic judgment or substantive content production. There are no formatting-only or checklist-only roles where speed matters more than depth.

---

## 5. Coordination: STUDY_STATE.md

Git captures file state but not learning state. `STUDY_STATE.md` tracks the learner's current topic, goals, curriculum progress, materials produced, and open questions. Every agent reads it on startup.

### What it contains

- **Current Topic** — what the learner is studying now and at what depth
- **Learning Goals** — what the learner wants to achieve
- **Curriculum** — ordered list of topics/subtopics with status and depth
- **Materials Produced** — paths and descriptions of created artifacts
- **Open Questions** — areas of confusion or topics to explore further
- **Last Agent Action** — what was done, by whom, on which branch

### Rules

1. The Orchestrator updates `STUDY_STATE.md` after every significant agent action
2. Every agent session loads `STUDY_STATE.md` as part of its context
3. Open questions from tutoring sessions feed back into Curriculum Designer decisions
4. Keep it under 500 tokens — compact after each major topic change

---

## 6. Git Workflow

### Branch strategy
```
main                                    # Stable, reviewed materials
│
├── researcher/quantum-mechanics        # Role/topic-description
├── content-creator/linear-algebra-cards
├── tutor/session-probability-basics
│
└── integration/machine-learning-unit   # Multi-role unit of study
```

### Rules

1. Never work on `main` directly — always branch
2. Commit messages explain WHY: `{role}: {description}`
3. Materials go through Critic review before merging to main (for deep-dive and curriculum-level work)
4. Quick-learn and tutoring sessions can merge with lighter review
5. Never squash commits — the revision history shows how understanding evolved

---

## 7. What This System Doesn't Have (and Why)

| Missing element | Why it's absent | When it would be needed |
|---|---|---|
| **Sprint cadence** | Study is ad-hoc, not time-boxed. "Learn calculus" doesn't fit a 2-week sprint. | If following a formal degree program with deadlines |
| **Librarian / Citation manager** | No publication requirements. The Researcher handles source management directly. | If producing publishable work (use the science team instead) |
| **Red Team** | Study materials don't need adversarial stress-testing. The Critic covers accuracy. | If preparing for a formal exam or certification with trick questions |
| **Editor / Formatter** | No submission formatting requirements. Content Creator handles formatting within materials. | If producing formatted reports for external consumption |
| **Presenter** | No audience beyond the learner. The Tutor handles explanation. | If preparing to teach others or give presentations |
| **Multiple quality roles** | One Critic is sufficient for personal study materials. | If materials will be shared publicly or used in a course |

---

## 8. Comparison: Study Team vs. Science Team vs. Tech Team

| Dimension | Study Team | Science Team | Tech Team |
|---|---|---|---|
| **Total roles** | 6 (fixed) | 10 (fixed) | 8–30 (scales) |
| **Hierarchy** | Flat (1 Orchestrator) | Flat (1 Orchestrator) | Hierarchical |
| **Process model** | Flexible routing | Deliverable-driven phases | Sprint-based agile |
| **Parallelism** | Low (mostly sequential) | Low-moderate | High |
| **Primary output** | Study materials + knowledge | One document (paper) | One product (app/service) |
| **Quality gates** | Critic review | PR-based peer review + human checkpoints | PR review + QA + security |
| **Workflow trigger** | User request (ad-hoc) | Phase progression | Sprint backlog |
| **Web access** | All roles | Limited (Researcher, Librarian) | Limited (relevant roles) |
| **Model tiers** | 2 (Opus, Sonnet) | 3 (Opus, Sonnet, Haiku) | 3 (Opus, Sonnet, Haiku) |
| **Stakeholder** | Single learner | Supervisor + journal reviewers | Users + business |

---

## 9. Context Management

### Subagent return summaries

When a subagent completes its task, it returns a summary to the Orchestrator — not its full output. Target 1,000–2,000 tokens covering:
- What was accomplished (materials produced, research completed)
- Key findings or decisions
- Open questions or follow-up suggestions
- What the next agent in the pipeline needs to know

### STUDY_STATE.md budget

Keep `STUDY_STATE.md` under 500 tokens. The Orchestrator compacts it when topics change — archiving completed curriculum items, collapsing finished materials, keeping only currently actionable state.

### Per-role context loading

| Role | Must load | On-demand |
|------|-----------|-----------|
| Curriculum Designer | STUDY_STATE.md, assigned task | Existing materials list |
| Researcher | STUDY_STATE.md, topic context, assigned task | Prior research on related topics |
| Tutor | STUDY_STATE.md, topic context, relevant materials | Research reports, curriculum |
| Content Creator | STUDY_STATE.md, research reports, learning objectives | Existing materials (to avoid duplication) |
| Critic | STUDY_STATE.md, materials under review | Source materials for fact-checking |
