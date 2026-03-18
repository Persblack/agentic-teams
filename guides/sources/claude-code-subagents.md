# Create Custom Subagents — Claude Code Documentation

> Source: https://code.claude.com/docs/en/sub-agents

Subagents are specialized AI assistants that handle specific types of tasks. Each subagent runs in its own context window with a custom system prompt, specific tool access, and independent permissions. When Claude encounters a task that matches a subagent's description, it delegates to that subagent, which works independently and returns results.

> If you need multiple agents working in parallel and communicating with each other, see agent teams instead. Subagents work within a single session; agent teams coordinate across separate sessions.

Subagents help you:

* **Preserve context** by keeping exploration and implementation out of your main conversation
* **Enforce constraints** by limiting which tools a subagent can use
* **Reuse configurations** across projects with user-level subagents
* **Specialize behavior** with focused system prompts for specific domains
* **Control costs** by routing tasks to faster, cheaper models like Haiku

## Built-in Subagents

### Explore
A fast, read-only agent optimized for searching and analyzing codebases.
- **Model**: Haiku (fast, low-latency)
- **Tools**: Read-only tools (denied access to Write and Edit tools)
- **Purpose**: File discovery, code search, codebase exploration

### Plan
A research agent used during plan mode to gather context before presenting a plan.
- **Model**: Inherits from main conversation
- **Tools**: Read-only tools
- **Purpose**: Codebase research for planning

### General-purpose
A capable agent for complex, multi-step tasks that require both exploration and action.
- **Model**: Inherits from main conversation
- **Tools**: All tools
- **Purpose**: Complex research, multi-step operations, code modifications

### Other Built-in Agents

| Agent | Model | When Claude uses it |
|:------|:------|:--------------------|
| Bash | Inherits | Running terminal commands in a separate context |
| statusline-setup | Sonnet | When you run `/statusline` to configure your status line |
| Claude Code Guide | Haiku | When you ask questions about Claude Code features |

## Configuration

### Subagent File Format

Subagent files use YAML frontmatter for configuration, followed by the system prompt in Markdown:

```markdown
---
name: code-reviewer
description: Reviews code for quality and best practices
tools: Read, Glob, Grep
model: sonnet
---

You are a code reviewer. When invoked, analyze the code and provide
specific, actionable feedback on quality, security, and best practices.
```

### Supported Frontmatter Fields

| Field | Required | Description |
|:------|:---------|:------------|
| `name` | Yes | Unique identifier using lowercase letters and hyphens |
| `description` | Yes | When Claude should delegate to this subagent |
| `tools` | No | Tools the subagent can use. Inherits all tools if omitted |
| `disallowedTools` | No | Tools to deny, removed from inherited or specified list |
| `model` | No | Model to use: `sonnet`, `opus`, `haiku`, a full model ID, or `inherit`. Defaults to `inherit` |
| `permissionMode` | No | Permission mode: `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, or `plan` |
| `maxTurns` | No | Maximum number of agentic turns before the subagent stops |
| `skills` | No | Skills to load into the subagent's context at startup |
| `mcpServers` | No | MCP servers available to this subagent |
| `hooks` | No | Lifecycle hooks scoped to this subagent |
| `memory` | No | Persistent memory scope: `user`, `project`, or `local` |
| `background` | No | Set to `true` to always run as a background task. Default: `false` |
| `isolation` | No | Set to `worktree` to run in a temporary git worktree |

### Scope and Priority

| Location | Scope | Priority |
|:---------|:------|:---------|
| `--agents` CLI flag | Current session | 1 (highest) |
| `.claude/agents/` | Current project | 2 |
| `~/.claude/agents/` | All your projects | 3 |
| Plugin's `agents/` directory | Where plugin is enabled | 4 (lowest) |

### Model Selection

- **Model alias**: `sonnet`, `opus`, or `haiku`
- **Full model ID**: e.g., `claude-opus-4-6` or `claude-sonnet-4-6`
- **inherit**: Use the same model as the main conversation
- **Omitted**: Defaults to `inherit`

### Permission Modes

| Mode | Behavior |
|:-----|:---------|
| `default` | Standard permission checking with prompts |
| `acceptEdits` | Auto-accept file edits |
| `dontAsk` | Auto-deny permission prompts |
| `bypassPermissions` | Skip all permission checks |
| `plan` | Plan mode (read-only exploration) |

### Persistent Memory

The `memory` field gives the subagent a persistent directory that survives across conversations.

| Scope | Location | Use when |
|:------|:---------|:---------|
| `user` | `~/.claude/agent-memory/<name>/` | Should remember learnings across all projects |
| `project` | `.claude/agent-memory/<name>/` | Knowledge is project-specific and shareable via VCS |
| `local` | `.claude/agent-memory-local/<name>/` | Project-specific but not checked into VCS |

### CLI-Defined Subagents

```bash
claude --agents '{
  "code-reviewer": {
    "description": "Expert code reviewer. Use proactively after code changes.",
    "prompt": "You are a senior code reviewer. Focus on code quality, security, and best practices.",
    "tools": ["Read", "Grep", "Glob", "Bash"],
    "model": "sonnet"
  }
}'
```

### Restricting Spawnable Subagents

Use `Agent(agent_type)` syntax in the `tools` field:

```yaml
---
name: coordinator
description: Coordinates work across specialized agents
tools: Agent(worker, researcher), Read, Bash
---
```

### Scoping MCP Servers

```yaml
---
name: browser-tester
description: Tests features in a real browser using Playwright
mcpServers:
  - playwright:
      type: stdio
      command: npx
      args: ["-y", "@playwright/mcp@latest"]
  - github
---
```

### Hooks

Subagents support lifecycle hooks defined in frontmatter:

| Event | When it fires |
|:------|:-------------|
| `PreToolUse` | Before the subagent uses a tool |
| `PostToolUse` | After the subagent uses a tool |
| `Stop` | When the subagent finishes |

Project-level hooks for subagent lifecycle events:

| Event | When it fires |
|:------|:-------------|
| `SubagentStart` | When a subagent begins execution |
| `SubagentStop` | When a subagent completes |

## Working with Subagents

### Foreground vs Background

- **Foreground**: Block main conversation until complete. Permission prompts passed through.
- **Background**: Run concurrently. Pre-approved permissions only. Auto-deny anything not pre-approved.

### Key Patterns

1. **Isolate high-volume operations** — tests, docs, log processing stay in subagent context
2. **Run parallel research** — multiple subagents explore different areas simultaneously
3. **Chain subagents** — sequential multi-step workflows

### Resuming Subagents

Resumed subagents retain full conversation history. Transcripts stored at `~/.claude/projects/{project}/{sessionId}/subagents/agent-{agentId}.jsonl`.

### Auto-Compaction

Triggers at ~95% capacity by default. Configurable via `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE`.

## Key Constraints

- Subagents **cannot spawn other subagents** (no nesting)
- Subagents receive only their system prompt + spawn prompt, NOT the parent's conversation history
- Subagents DO inherit CLAUDE.md files, MCP servers, and project settings
- Max ~10 concurrent subagents
