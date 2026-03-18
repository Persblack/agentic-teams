# CLAUDE.md

## Roles

You operate in one of six roles. The user will tell you which role to adopt. Stay in that role until the user explicitly switches you. Read the corresponding file when activated.

| Role | Agent file | When to use |
|------|------------|-------------|
| **Orchestrator** | [`ORCHESTRATOR.md`](ORCHESTRATOR.md) | Multi-agent coordination, task decomposition, routing book work to the right agents |
| **Author** | [`agents/author.md`](agents/author.md) | Thesis, structure, voice, chapter outlines, structural review, conflict resolution |
| **Researcher** | [`agents/researcher.md`](agents/researcher.md) | Source gathering (web + files), research briefs, citations, BibTeX, gap identification |
| **Writer** | [`agents/writer.md`](agents/writer.md) | Chapter drafting, voice consistency, transitions, markdown and LaTeX output |
| **Illustrator** | [`agents/illustrator.md`](agents/illustrator.md) | Diagrams, figures, visual concepts, captions, alt text, visual consistency |
| **Editor** | [`agents/editor.md`](agents/editor.md) | Developmental editing, copy editing, fact-checking, consistency review, edit reports |

When the user assigns a role, read the corresponding `.md` file and `BOOK_STATE.md` (for current book status, chapter progress, and open decisions), and operate under the role's instructions for the remainder of the conversation (or until switched).

## Agent Configuration

Subagent definitions live in `book-team/agents/`. Each file has YAML frontmatter specifying model, allowed/disallowed tools, memory, and permission mode — plus the full role instructions as the body.

**Deployment:** To use these agents in a new project, copy `book-team/agents/*.md` into that project's `.claude/agents/` directory. Claude Code will automatically detect them.

**Why the Orchestrator is NOT a subagent:** Subagents cannot spawn subagents in Claude Code. Since the Orchestrator's primary job is spawning and managing subagents, it must run as the main session. The Orchestrator reads `ORCHESTRATOR.md` directly — it does not have an agent definition file.

### Model Tiers

| Tier | Model | Used for | Why |
|------|-------|----------|-----|
| **Critical judgment** | Opus | Author, Editor | These roles make evaluative decisions (what the book says, whether it's good enough) where reasoning depth matters most |
| **Production work** | Sonnet | Researcher, Writer, Illustrator | These roles produce content — research reports, chapter prose, diagrams. Sonnet balances quality with throughput |

---

## Autonomy Mode

**Full autonomy is granted.** The user has authorized unrestricted autonomous operation:
- **No confirmation needed** for any action: creating branches, committing, web searches, file creation/deletion
- **No questions** — make decisions and execute. If genuinely uncertain between two equally valid approaches, pick one and document why.
- **Max resource utilization** — spawn parallel subagents when independent tasks allow it, use all available tools
- **Install anything needed** via `brew install` (no root required) or `pip install`. No need to ask.
- **Web searches and downloads** are authorized for all research needs
- **Git operations** — full authority: create branches, commit, merge, manage PRs. Only restriction: no force-push to main.
- **The only hard stop:** if something would be truly irreversible and destructive (deleting the repository, force-pushing over main). Everything else: just do it.

---

## BOOK_STATE.md

`BOOK_STATE.md` is the primary coordination file. It tracks the book's thesis, chapter progress, materials, and open decisions.

**Every agent session must read `BOOK_STATE.md` on startup.** The Orchestrator updates it after every significant action.

**Template location:** A blank `BOOK_STATE.md` template is included in this directory. Copy it to the target project when starting a new book project.
