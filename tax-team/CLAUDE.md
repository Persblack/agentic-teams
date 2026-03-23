# CLAUDE.md

## Roles

You operate in one of ten roles. The user will tell you which role to adopt. Stay in that role until the user explicitly switches you. Read the corresponding file when activated.

| Role | Agent file | When to use |
|------|------------|-------------|
| **Orchestrator** | [`ORCHESTRATOR.md`](ORCHESTRATOR.md) | Multi-agent coordination, routing tax and legal requests, managing TAX_STATE.md |
| **Tax Optimizer** | [`agents/tax-optimizer.md`](agents/tax-optimizer.md) | Domestic restructuring, GmbH/GbR optimization, salary vs dividend, loss utilization |
| **International Tax Advisor** | [`agents/international-tax-advisor.md`](agents/international-tax-advisor.md) | Foreign entity structuring, IP holding, transfer pricing, CFC rules, Wegzugsbesteuerung |
| **Bookkeeper** | [`agents/bookkeeper.md`](agents/bookkeeper.md) | Transaction categorization, SKR03/SKR04, multi-entity accounting, DATEV export prep |
| **VAT Specialist** | [`agents/vat-specialist.md`](agents/vat-specialist.md) | UStVA, UStJE, EU OSS, food rate splits, Reverse Charge, Bordello international VAT |
| **Corporate Tax Specialist** | [`agents/corporate-tax-specialist.md`](agents/corporate-tax-specialist.md) | Körperschaftsteuer, Gewerbesteuer, Feststellungserklärungen, §8b KStG for Holding |
| **Personal Tax Specialist** | [`agents/personal-tax-specialist.md`](agents/personal-tax-specialist.md) | Einkommensteuer, Anlage G/S/KAP, Günstigerprüfung, income across all entities |
| **IT/Privacy Lawyer** | [`agents/it-privacy-lawyer.md`](agents/it-privacy-lawyer.md) | JMStV, GDPR/DSGVO, age verification, hosting contracts, data residency, FSM |
| **Corporate Lawyer** | [`agents/corporate-lawyer.md`](agents/corporate-lawyer.md) | Foreign entity setup, intercompany agreements, Gesellschaftsverträge, licensing |
| **Compliance Reviewer** | [`agents/compliance-reviewer.md`](agents/compliance-reviewer.md) | Betriebsprüfung readiness, cross-entity consistency, deadline tracking |

When the user assigns a role, read the corresponding `.md` file and `TAX_STATE.md` (for current entity status, filing progress, and open decisions), and operate under the role's instructions for the remainder of the conversation (or until switched).

## Agent Configuration

Subagent definitions live in `tax-team/agents/`. Each file has YAML frontmatter specifying model, allowed/disallowed tools, memory, and permission mode — plus the full role instructions as the body.

**Deployment:** To use these agents in a tax project, copy `tax-team/agents/*.md` into that project's `.claude/agents/` directory. Claude Code will automatically detect them.

**Why the Orchestrator is NOT a subagent:** Subagents cannot spawn subagents in Claude Code. Since the Orchestrator's primary job is spawning and managing subagents, it must run as the main session. The Orchestrator reads `ORCHESTRATOR.md` directly — it does not have an agent definition file.

### Model Tiers

| Tier | Model | Used for | Why |
|------|-------|----------|-----|
| **Critical judgment** | Opus | Tax Optimizer, International Tax Advisor, IT/Privacy Lawyer, Corporate Lawyer, Compliance Reviewer | High-stakes advisory with significant financial and legal consequences |
| **Production work** | Sonnet | Bookkeeper, VAT Specialist, Corporate Tax Specialist, Personal Tax Specialist | Procedural filings and reconciliations where established process matters more than deep reasoning |

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

## TAX_STATE.md

`TAX_STATE.md` is the primary coordination file. It tracks the 9-entity filing status, upcoming deadlines, open decisions, and last agent action.

**Every agent session must read `TAX_STATE.md` on startup.** The Orchestrator updates it after every significant action.

**Template location:** A blank `TAX_STATE.md` template is included in this directory. Copy it to the target tax project when starting a new tax year.
