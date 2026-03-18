# Building Agents with the Claude Agent SDK

> Source: https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk
> Published: September 29, 2025

## Last Year's Lessons, Today's Vision

Last year, Anthropic shared lessons in building effective agents alongside customers. Since then, Claude Code launched as an agentic coding solution originally built to support developer productivity at Anthropic.

Over recent months, Claude Code has evolved far beyond a coding tool. Teams at Anthropic have deployed it for deep research, video creation, and note-taking. It now powers almost all major agent loops within the company.

The agent infrastructure underlying Claude Code — the Claude Code SDK — can power many other agent types. To reflect this broader vision, the tool is being renamed to the Claude Agent SDK.

## Giving Claude a Computer

The core design principle behind Claude Code centers on providing Claude with the same tools programmers use daily: finding files in codebases, writing and editing files, linting code, running it, debugging, and iterating until success.

By granting Claude access to the user's computer via terminal, it gained the ability to write code like programmers do. This capability extends beyond coding. With bash commands, file editing, file creation, and file search tools, Claude can read CSV files, search the web, build visualizations, interpret metrics, and perform general-purpose digital work — creating general-purpose agents with computer access.

The foundational principle: "give your agents a computer, allowing them to work like humans do."

## Creating New Types of Agents

The SDK unlocks building more effective agents. Developers can now create:

- **Finance agents:** Understand portfolios and investment goals by accessing external APIs, storing data, and running calculations.
- **Personal assistant agents:** Book travel, manage calendars, schedule appointments, and prepare briefs by connecting to internal data sources and tracking context across applications.
- **Customer support agents:** Handle ambiguous requests by collecting user data, connecting to APIs, messaging users, and escalating to humans when needed.
- **Deep research agents:** Conduct comprehensive research across large document collections by searching file systems, analyzing multiple sources, cross-referencing data, and generating reports.

## Building Your Agent Loop

In Claude Code, Claude operates within a specific feedback loop: **gather context -> take action -> verify work -> repeat**.

### Gather Context

Agents need more than prompts — they must fetch and update their own context.

#### Agentic Search and the File System

The file system represents information that could be pulled into the model's context. When encountering large files like logs or user uploads, Claude decides how to load them using bash scripts like `grep` and `tail`. The folder and file structure becomes a form of context engineering.

#### Semantic Search

Semantic search is faster than agentic search but less accurate, more difficult to maintain, and less transparent. It involves chunking relevant context, embedding chunks as vectors, and searching concepts through vector queries.

Recommendation: Start with agentic search, adding semantic search only if faster results or more variations are needed.

#### Subagents

The Claude Agent SDK supports subagents by default. They enable two capabilities:

1. **Parallelization:** Spin up multiple subagents working on different tasks simultaneously
2. **Context management:** Subagents use isolated context windows, returning only relevant information to the orchestrator rather than full context

#### Compaction

For long-running agents, context maintenance becomes critical. The SDK's compact feature automatically summarizes previous messages as the context limit approaches, preventing agents from running out of context.

### Take Action

Once context is gathered, agents need flexible ways to take action.

#### Tools

Tools are the primary building blocks of agent execution. They appear prominently in Claude's context window, making them the primary actions Claude considers. Design tools consciously to maximize context efficiency.

#### Bash & Scripts

Bash serves as a general-purpose tool allowing flexible computer work.

#### Code Generation

The Claude Agent SDK excels at code generation. Code is precise, composable, and infinitely reusable — ideal for agents performing complex operations reliably.

Recent file creation in Claude.AI relies entirely on code generation. Claude writes Python scripts to create Excel spreadsheets, PowerPoint presentations, and Word documents.

#### MCPs

The Model Context Protocol (MCP) provides standardized integrations to external services, handling authentication and API calls automatically. Connect agents to tools like Slack, GitHub, Google Drive, or Asana without writing custom integration code.

### Verify Your Work

The Claude Code SDK completes the agentic loop by evaluating work. Agents that check and improve their own output are fundamentally more reliable.

#### Defining Rules

Providing clearly defined rules for output, then explaining which rules failed, is effective feedback. Code linting provides excellent rules-based feedback.

#### Visual Feedback

When completing visual tasks like UI generation or testing, visual feedback through screenshots or renders helps.

#### LLM as a Judge

Have another language model "judge" agent output based on fuzzy rules. This isn't very robust and carries heavy latency tradeoffs, but for applications where any performance boost justifies the cost, it can help.

## Testing and Improving Your Agent

After several loops through the agent cycle, test and ensure the agent is well-equipped. The best improvement path involves carefully examining output, especially failures, and asking: does it have the right tools?

- **Misunderstanding tasks?** The agent might lack key information. Can you alter search APIs to make finding necessary knowledge easier?
- **Repeated failures?** Can you add formal rules in tool calls to identify and fix failures?
- **Can't fix errors?** Can you provide more useful or creative tools to approach problems differently?
- **Performance varies?** Build representative test sets for programmatic evaluations based on customer usage.
