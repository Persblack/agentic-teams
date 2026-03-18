# Equipping Agents for the Real World with Agent Skills

> Source: https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills

## Overview

Anthropic has introduced **Agent Skills**, a framework for building specialized AI agents through organized folders containing instructions, scripts, and resources. This approach transforms general-purpose agents like Claude Code into domain-specific tools by packaging expertise into composable, discoverable capabilities.

## What Are Agent Skills?

Agent Skills extend Claude's capabilities by bundling procedural knowledge into reusable resources. "Instead of building fragmented, custom-designed agents for each use case, anyone can now specialize their agents with composable capabilities by capturing and sharing their procedural knowledge."

The fundamental structure consists of a directory containing a `SKILL.md` file — the entry point that must include YAML frontmatter with required metadata (`name` and `description`).

## Anatomy of a Skill

### Progressive Disclosure Design

Skills employ a three-level disclosure model:

1. **Level One**: Skill metadata (name and description) loaded into the system prompt at startup
2. **Level Two**: Full `SKILL.md` content loaded when Claude determines the skill is relevant
3. **Level Three and Beyond**: Additional linked files referenced from `SKILL.md`, loaded only when needed

This hierarchical structure prevents context window bloat. "Agents with a filesystem and code execution tools don't need to read the entirety of a skill into their context window when working on a particular task."

### Real-World Example: PDF Skill

The PDF skill demonstrates the concept. It includes:
- Core `SKILL.md` with primary instructions
- `reference.md` for general PDF handling
- `forms.md` for form-specific operations

Claude reads `forms.md` only when actually filling out forms, keeping the core skill lean.

## Context Window Integration

When a skill is triggered:

1. The initial context includes core system prompt, skill metadata, and the user's message
2. Claude invokes Bash tools to read the `SKILL.md` file
3. Claude selectively reads additional bundled files as needed
4. The agent proceeds with the task using loaded skill instructions

## Code Execution Within Skills

Skills can bundle executable code — typically Python scripts — that Claude runs as tools rather than loading into context. "Large language models excel at many tasks, but certain operations are better suited for traditional code execution. For example, sorting a list via token generation is far more expensive than simply running a sorting algorithm."

## Development Best Practices

**Start with evaluation**: Identify capability gaps through representative task testing, then build skills incrementally.

**Structure for scale**: Split unwieldy `SKILL.md` files into separate documents, keeping mutually exclusive contexts separate to reduce token usage.

**Think from Claude's perspective**: Monitor real-world skill usage, paying special attention to how skill names and descriptions influence triggering decisions.

**Iterate with Claude**: Collaborate with Claude to capture successful approaches into reusable skill components, discovering actual context needs rather than anticipating them.

## Security Considerations

Skills introduce new capabilities but also potential vulnerabilities. "Install skills only from trusted sources. When installing a skill from a less-trusted source, thoroughly audit it before use." Particular attention should go to code dependencies, bundled resources, and instructions directing Claude toward external network sources.

## Current and Future Availability

Agent Skills are currently supported across:
- Claude.ai
- Claude Code
- Claude Agent SDK
- Claude Developer Platform

Future enhancements will support the complete skill lifecycle (creation, editing, discovery, sharing, usage). The framework's simplicity — based on folders and files — lowers barriers for organizations and developers building customized agents.
