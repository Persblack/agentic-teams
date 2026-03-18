# Effective Harnesses for Long-Running Agents

> Source: https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents

## Overview

As AI agents become increasingly capable, developers are asking them to handle complex tasks spanning hours or days. However, maintaining consistent progress across multiple context windows remains challenging. The core issue: agents work in discrete sessions, with each new session starting without memory of prior work.

## The Core Problem

Long-running agents face a fundamental challenge. Context windows are limited, and most complex projects cannot be completed within a single window. Like engineers working in shifts with no handoff documentation, agents must "bridge the gap between coding sessions."

Anthropic observed two primary failure patterns:

**Pattern 1: Over-ambitious implementation.** Agents attempted to build entire applications at once, running out of context mid-implementation. The next session inherited half-finished, undocumented features, forcing substantial recovery work.

**Pattern 2: Premature completion.** Later agent instances surveyed completed work and declared the project finished, missing remaining features.

## The Two-Part Solution

Anthropic developed a framework using specialized prompts for different phases:

### 1. Initializer Agent

The first session uses specialized instructions to establish foundational infrastructure:

- **init.sh script** — Enables automated development server startup
- **claude-progress.txt file** — Maintains a log of agent work
- **Initial git commit** — Documents added files

### 2. Coding Agent

Subsequent sessions receive different prompting emphasizing incremental progress while maintaining clean, merge-ready code.

## Environment Management Strategies

### Feature List Architecture

The initializer creates a comprehensive JSON-formatted feature specification. For a claude.ai clone, this expanded to 200+ features like:

```json
{
    "category": "functional",
    "description": "New chat button creates a fresh conversation",
    "steps": [
      "Navigate to main interface",
      "Click the 'New Chat' button",
      "Verify a new conversation is created",
      "Check that chat area shows welcome state",
      "Verify conversation appears in sidebar"
    ],
    "passes": false
}
```

Agents may only modify the `passes` field. This prevents accidental feature removal while providing clear implementation targets. JSON format reduces model-induced corruption compared to Markdown.

### Incremental Progress

Working on single features per session proved critical. Agents commit progress with descriptive messages and update progress files, enabling version control recovery from mistakes.

### Testing Requirements

Claude initially marked features complete without proper verification. Explicit prompting to use browser automation tools (Puppeteer MCP) for end-to-end testing dramatically improved quality. Agents perform manual user workflows rather than relying solely on unit tests.

## Session Startup Protocol

Each coding session follows this sequence:

1. Run `pwd` to confirm working directory
2. Read git logs and progress files
3. Review feature list and select highest-priority incomplete feature
4. Start development server using init.sh
5. Run basic end-to-end verification
6. Begin feature implementation

A typical session starts with:

```
[Assistant] I'll start by getting my bearings and understanding the current state of the project.
[Tool Use] <bash - pwd>
[Tool Use] <read - claude-progress.txt>
[Tool Use] <read - feature_list.json>
[Assistant] Let me check the git log to see recent work.
[Tool Use] <bash - git log --oneline -20>
[Assistant] Now let me check if there's an init.sh script to restart the servers.
<Starts the development server>
```

## Common Failure Modes and Solutions

| Problem | Initializer Approach | Coding Agent Approach |
|---------|----------------------|----------------------|
| Premature project completion | Create comprehensive feature list | Select one feature per session |
| Buggy or undocumented environments | Establish git repo and progress notes | Verify functionality before new work |
| Features marked complete prematurely | Define feature requirements | Test thoroughly before marking complete |
| Time wasted on setup | Write init.sh startup script | Execute init.sh immediately |

## Known Limitations

Some challenges persist. Claude cannot see browser-native alert modals through Puppeteer MCP, causing modal-dependent features to be buggier. Vision limitations also restrict bug identification to observable interface changes.

## Future Directions

Current research demonstrates one solution set, but questions remain:

- **Multi-agent architecture**: Could specialized agents (testing, QA, cleanup) outperform single general-purpose agents?
- **Domain generalization**: These techniques were optimized for full-stack web development. Can findings apply to scientific research or financial modeling?
