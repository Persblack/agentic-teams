# Subagents in the Claude Agent SDK

> Source: https://platform.claude.com/docs/en/agent-sdk/subagents

Define and invoke subagents to isolate context, run tasks in parallel, and apply specialized instructions in your Claude Agent SDK applications.

## Overview

You can create subagents in three ways:

- **Programmatically**: use the `agents` parameter in your `query()` options
- **Filesystem-based**: define agents as markdown files in `.claude/agents/` directories
- **Built-in general-purpose**: Claude can invoke the built-in `general-purpose` subagent at any time via the Agent tool

## Benefits of Using Subagents

### Context Isolation
Each subagent runs in its own fresh conversation. Intermediate tool calls and results stay inside the subagent; only its final message returns to the parent.

### Parallelization
Multiple subagents can run concurrently, dramatically speeding up complex workflows.

### Specialized Instructions and Knowledge
Each subagent can have tailored system prompts with specific expertise, best practices, and constraints.

### Tool Restrictions
Subagents can be limited to specific tools, reducing the risk of unintended actions.

## Creating Subagents Programmatically

### Python

```python
import asyncio
from claude_agent_sdk import query, ClaudeAgentOptions, AgentDefinition

async def main():
    async for message in query(
        prompt="Review the authentication module for security issues",
        options=ClaudeAgentOptions(
            allowed_tools=["Read", "Grep", "Glob", "Agent"],
            agents={
                "code-reviewer": AgentDefinition(
                    description="Expert code review specialist.",
                    prompt="""You are a code review specialist with expertise in security, performance, and best practices.

When reviewing code:
- Identify security vulnerabilities
- Check for performance issues
- Verify adherence to coding standards
- Suggest specific improvements""",
                    tools=["Read", "Grep", "Glob"],
                    model="sonnet",
                ),
                "test-runner": AgentDefinition(
                    description="Runs and analyzes test suites.",
                    prompt="""You are a test execution specialist. Run tests and provide clear analysis of results.""",
                    tools=["Bash", "Read", "Grep"],
                ),
            },
        ),
    ):
        if hasattr(message, "result"):
            print(message.result)

asyncio.run(main())
```

### TypeScript

```typescript
import { query } from "@anthropic-ai/claude-agent-sdk";

for await (const message of query({
  prompt: "Review the authentication module for security issues",
  options: {
    allowedTools: ["Read", "Grep", "Glob", "Agent"],
    agents: {
      "code-reviewer": {
        description: "Expert code review specialist.",
        prompt: `You are a code review specialist...`,
        tools: ["Read", "Grep", "Glob"],
        model: "sonnet"
      },
      "test-runner": {
        description: "Runs and analyzes test suites.",
        prompt: `You are a test execution specialist...`,
        tools: ["Bash", "Read", "Grep"]
      }
    }
  }
})) {
  if ("result" in message) console.log(message.result);
}
```

### AgentDefinition Configuration

| Field | Type | Required | Description |
|:------|:-----|:---------|:------------|
| `description` | `string` | Yes | When to use this agent |
| `prompt` | `string` | Yes | The agent's system prompt |
| `tools` | `string[]` | No | Allowed tool names. Inherits all if omitted |
| `model` | `'sonnet' \| 'opus' \| 'haiku' \| 'inherit'` | No | Model override. Defaults to main model |

**Note:** Subagents cannot spawn their own subagents. Don't include `Agent` in a subagent's `tools` array.

## What Subagents Inherit

| Receives | Does NOT receive |
|:---------|:-----------------|
| Its own system prompt + Agent tool's prompt | Parent's conversation history or tool results |
| Project CLAUDE.md (via settingSources) | Skills (unless listed in AgentDefinition.skills) |
| Tool definitions (inherited or subset) | Parent's system prompt |

## Invoking Subagents

### Automatic Invocation
Claude automatically decides when to invoke subagents based on the task and each subagent's `description`.

### Explicit Invocation
Mention by name: `"Use the code-reviewer agent to check the authentication module"`

### Dynamic Agent Configuration

```python
def create_security_agent(security_level: str) -> AgentDefinition:
    is_strict = security_level == "strict"
    return AgentDefinition(
        description="Security code reviewer",
        prompt=f"You are a {'strict' if is_strict else 'balanced'} security reviewer...",
        tools=["Read", "Grep", "Glob"],
        model="opus" if is_strict else "sonnet",
    )
```

## Detecting Subagent Invocation

Check for `tool_use` blocks where `name` is `"Agent"`. Messages from within a subagent's context include a `parent_tool_use_id` field.

**Note:** The tool name was renamed from `"Task"` to `"Agent"` in Claude Code v2.1.63.

## Resuming Subagents

Subagents can be resumed to continue where they left off with full conversation history. Flow:

1. Capture `session_id` from messages during first query
2. Extract `agentId` from message content
3. Pass `resume: sessionId` in second query's options

## Tool Restrictions

### Common Tool Combinations

| Use case | Tools | Description |
|:---------|:------|:------------|
| Read-only analysis | `Read`, `Grep`, `Glob` | Examine code but not modify or execute |
| Test execution | `Bash`, `Read`, `Grep` | Run commands and analyze output |
| Code modification | `Read`, `Edit`, `Write`, `Grep`, `Glob` | Full read/write without command execution |
| Full access | All tools | Omit `tools` field to inherit all |

## Troubleshooting

- **Claude not delegating**: Include `Agent` in `allowedTools`, use explicit prompting, write clear descriptions
- **Filesystem agents not loading**: Restart session after adding agent files
- **Windows long prompt failures**: Keep prompts concise or use filesystem-based agents
