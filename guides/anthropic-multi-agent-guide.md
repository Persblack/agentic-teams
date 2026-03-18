# Anthropic Multi-Agent Patterns & Resources Guide

> Compiled reference for building agentic team systems with Claude. Covers official Anthropic patterns, Claude Code subagents, Agent SDK, context engineering, and production harnesses.

---

## 1. Workflows vs. Agents (Foundational Distinction)

Anthropic draws a sharp line:

- **Workflows**: LLMs orchestrated through *predefined code paths* — predictable, consistent, suited for well-defined tasks.
- **Agents**: LLMs that *dynamically direct their own processes* and tool usage — flexible, suited when model-driven decision-making is needed at scale.

**Core recommendation**: Start with the simplest solution. Only add agentic complexity when simpler approaches demonstrably fail. Agents trade latency and cost for better task performance.

---

## 2. Six Agentic Patterns

From Anthropic's "Building Effective Agents" guide:

### Pattern 1: Prompt Chaining
Sequential steps, each LLM call processes previous output. Best for fixed subtasks (e.g., generate copy then translate).

### Pattern 2: Routing
Classify input, direct to specialized handlers. Can route simple queries to Haiku and complex ones to Sonnet/Opus.

### Pattern 3: Parallelization
Two variants:
- **Sectioning**: independent subtasks run simultaneously
- **Voting**: identical tasks run multiple times for diverse outputs

### Pattern 4: Orchestrator-Workers
Central LLM dynamically breaks down tasks, delegates to workers, synthesizes results. Key difference from parallelization: subtasks are *not pre-defined* but determined dynamically by the orchestrator.

### Pattern 5: Evaluator-Optimizer
One LLM generates, another evaluates iteratively. Best when clear evaluation criteria exist.

### Pattern 6: Autonomous Agents
LLMs plan and operate with tool usage in feedback loops. Require trust in model decision-making and extensive sandboxed testing.

---

## 3. Claude Code Subagents

### Architecture & Isolation

- Each subagent runs in its own **200K-token context window**
- Subagents execute **independently and in parallel** up to ~10 concurrent agents
- **Cannot be nested** — subagents cannot spawn other subagents (prevents infinite recursion)
- Results return to the main agent as tool results; subagents never communicate directly with each other

### Isolation Modes

| Mode | Behavior |
|------|----------|
| **Default** | Subagent runs in main session's filesystem/directory |
| **Worktree** (`isolation: worktree`) | Temporary git worktree with isolated copy of repo, auto-cleaned if no changes |
| **Background** | Runs as background task while main agent continues; needs pre-approved permissions |

### Subagent Configuration

Defined in Markdown files with YAML frontmatter at:
- `.claude/agents/` (project-level, shared via version control)
- `~/.claude/agents/` (user-level, all projects)
- CLI `--agents` flag (session-only, not persisted)

### Context Passing

- Subagents load the **same CLAUDE.md files, MCP servers, and skills** as the parent session
- They do **NOT** inherit the parent's conversation history
- Parent sends only a spawn prompt; subagent operates with this + its own system prompt

### Best Practices

- Keep each subagent focused on one task
- Limit tool access via `tools` and `disallowedTools` fields
- Use read-only subagents for investigation to keep output out of main context
- Chain subagents from main conversation for multi-step workflows
- Summarize results before returning to main context to preserve tokens

### Built-in Subagents

| Agent | Purpose | Model |
|-------|---------|-------|
| **Explore** | Fast, read-only code search | Haiku |
| **Plan** | Safe analysis in plan mode | Inherited |
| **General-purpose** | Complex multi-step work with full tools | Inherited |

---

## 4. Agent Teams (Experimental)

### Architecture

- **Multiple independent Claude Code sessions** that communicate directly
- Each teammate has its own **1M-token context window**
- Share a **task list and mailbox** for coordination
- **Lead agent** orchestrates, but teammates can message each other directly

### Enabling

```json
// settings.json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

### Best Practices

- Start with 3-5 teammates (balance parallelism with coordination overhead)
- Size tasks with 5-6 per teammate
- Use for independent work that benefits from different lenses
- Avoid file conflicts by assigning different files to different teammates
- Spawn with competing hypotheses for debugging (teammates debate theories)

### Limitations

- No session resumption with in-process teammates (`/resume` breaks)
- Task status can lag; may need manual updates
- Teammates cannot spawn their own teams (no nesting)
- One team per lead session

---

## 5. Claude Agent SDK

Available for **Python** and **TypeScript**. Same agent harness that powers Claude Code.

### Core Agent Loop

```
Gather Context → Take Action → Verify Work → Repeat
```

### Key Capabilities

| Feature | Description |
|---------|-------------|
| **Subagents** | Spawn isolated instances with own context windows |
| **Tool restrictions** | Limit what each subagent can do |
| **Model selection** | Route cheap tasks to Haiku, complex to Opus |
| **Parallel execution** | Multiple subagents concurrently |
| **Hooks** | Lifecycle callbacks (PreToolUse, PostToolUse, SubagentStart, etc.) |
| **Sessions** | Persist and resume context across queries |
| **MCP integration** | Standardized tool integrations |
| **Resumable subagents** | Resume with full conversation history |

### Programmatic API (Python)

```python
from claude_agent_sdk import query, ClaudeAgentOptions, AgentDefinition

async for message in query(
    prompt="Review the authentication module",
    options=ClaudeAgentOptions(
        allowed_tools=["Read", "Grep", "Glob", "Agent"],
        agents={
            "code-reviewer": AgentDefinition(
                description="Expert code review specialist.",
                prompt="You are a code review specialist...",
                tools=["Read", "Grep", "Glob"],
                model="sonnet",
            ),
        },
    ),
):
    ...
```

---

## 6. Context Engineering

Beyond prompt engineering — curating the optimal set of tokens during inference.

### Principles

- **Attention is finite** — longer contexts stretch model capabilities thin ("context rot")
- **Just-in-time context loading** — maintain lightweight identifiers (file paths, URLs), dynamically retrieve at runtime
- **Compaction** — summarize conversation contents and reinitiate with compressed context when approaching limits
- **Structured note-taking** — agents write persistent notes outside the context window (NOTES.md, MEMORY.md)
- **Sub-agent architectures** — specialized sub-agents explore extensively (tens of thousands of tokens), return condensed summaries (1,000-2,000 tokens)

---

## 7. Anthropic's Multi-Agent Research System

Their production system architecture:

### Pattern

1. **Lead Researcher** (Opus) receives user query
2. **Analyzes** request and develops strategy
3. **Spawns subagents** (Sonnet) with specific research mandates
4. **Subagents operate in parallel** with own contexts + tools
5. **CitationAgent** separately maps claims to sources
6. **Lead synthesizes** findings from all subagents

### Performance Data

| Metric | Value |
|--------|-------|
| Multi-agent vs single-agent | **90.2% better** (Opus+Sonnet vs single Opus) |
| Token usage | Agents use ~4x more than chat; multi-agent ~15x |
| Time savings | Parallelization cut research time by **up to 90%** |
| Performance variance | **80% explained by token usage**, rest by tool calls and model choice |

### Task Decomposition Guidance

| Query complexity | Agents | Tool calls per agent |
|-----------------|--------|---------------------|
| Simple fact-check | 1 | 3-10 |
| Direct comparison | 2-4 | 10-15 each |
| Complex research | 10+ | Clearly divided responsibilities |

---

## 8. Long-Running Agent Harnesses

For work spanning multiple context windows:

### Two-Agent Approach

- **Initializer Agent**: runs once to set up environment
- **Coding Agent**: runs repeatedly across sessions, making incremental progress

### State Continuity Mechanisms

- Progress documentation files
- Git history
- Feature registries
- Structured checkpoints

---

## 9. Model Tiering Recommendations

```
Task type                          Recommended model
─────────────────────────────────────────────────────
Fast lookups, listing              Haiku (low cost)
Standard development work          Sonnet (balanced)
Complex debugging, refactoring     Opus (most capable)
Lead agent in orchestrated system  Opus or Sonnet
Subagent doing review/research     Haiku or Sonnet (fast, focused)
```

---

## 10. Tool Design Principles (Agent-Computer Interface)

- Tools deserve equal prompt engineering attention as the overall prompt
- Use **poka-yoke principles** — structure arguments to make mistakes harder
- Ensure tool outputs are **token-efficient** — return information optimally sized for context consumption
- Each tool needs a **distinct purpose and clear description**; avoid overlap

---

## 11. Testing & Evaluation

- Start with ~20 test queries representing real usage patterns
- Use **LLM-as-judge** evaluation (single LLM scoring against criteria)
- Claude models can **diagnose their own failures** and suggest prompt improvements (40% decrease in task completion time)
- Use **rainbow deployments** to gradually shift traffic between versions

---

## 12. Limits & Gotchas

| Limit | Value | Notes |
|-------|-------|-------|
| Subagent context | 200K tokens | Same as main session |
| Agent team context | 1M tokens per teammate | Separate independent windows |
| Concurrent subagents | ~10 max | Later batches queue |
| Nesting depth | 1 level max | Subagents cannot spawn subagents |
| Team size | No hard limit | Practical: 3-5 recommended |

### Token Multipliers

- **Subagents**: ~1-2x main session tokens (results summarized back)
- **Agent teams**: ~4-7x main session tokens (each teammate = full context window)

### Known Issues

1. Session resumption breaks with in-process teammates
2. Subagent transcripts stored separately at `~/.claude/projects/{project}/{sessionId}/subagents/`
3. Cannot nest subagents — no recursive delegation
4. Automatic compaction may lose early instructions (put in CLAUDE.md instead)
5. File conflicts in agent teams if multiple agents edit same file

---

## 13. Decision Trees

### Subagents vs. Agent Teams

```
Do teammates need to talk to each other?
  ├─ NO → Subagents (faster, cheaper, simpler)
  └─ YES → Agent Teams (debate, collaboration, direct messaging)

Is the work independent and self-contained?
  ├─ YES → Subagents or Teams (parallel work)
  └─ NO → Sequential subagents (chain from main)

Token budget?
  ├─ Need to minimize → Subagents
  └─ Budget allows parallelism → Agent Teams
```

---

## Source Documents

All source documents are saved in `guides/sources/`. See the index below:

| Document | Local file |
|----------|-----------|
| Building Effective Agents | `sources/building-effective-agents.md` |
| Multi-Agent Research System | `sources/multi-agent-research-system.md` |
| Context Engineering | `sources/context-engineering.md` |
| Long-Running Agent Harnesses | `sources/effective-harnesses.md` |
| Building Agents with Agent SDK | `sources/building-agents-with-sdk.md` |
| Agent Skills | `sources/agent-skills.md` |
| Claude Code Subagents Docs | `sources/claude-code-subagents.md` |
| Agent SDK Subagents Docs | `sources/agent-sdk-subagents.md` |
| Anthropic Cookbook Agent Patterns | `sources/cookbook-agent-patterns.md` |
