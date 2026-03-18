# Effective Context Engineering for AI Agents

> Source: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
> Published: September 29, 2025

## Introduction

The field is shifting from prompt engineering to **context engineering** — the practice of optimizing the tokens available to language models. "Context refers to the set of tokens included when sampling from a large-language model (LLM)." The engineering challenge involves "optimizing the utility of those tokens against the inherent constraints of LLMs."

## Context Engineering vs. Prompt Engineering

While prompt engineering focuses on writing effective instructions, context engineering addresses a broader scope: "the set of strategies for curating and maintaining the optimal set of tokens (information) during LLM inference, including all the other information that may land there outside of the prompts."

As agents operate across multiple turns and longer timeframes, managing the entire context state — system instructions, tools, Model Context Protocol (MCP), external data, and message history — becomes essential.

## Why Context Engineering Matters

### The Attention Budget Problem

Research on needle-in-a-haystack benchmarking has revealed "context rot": as context windows grow, model recall degrades. Like humans with "limited working memory capacity," LLMs have finite attention budgets. "Every new token introduced depletes this budget by some amount."

### Architectural Constraints

The transformer architecture enables every token to attend to every other token, creating n-squared pairwise relationships. This produces "a natural tension between context size and attention focus." Models trained on predominantly shorter sequences have less experience with long-range dependencies.

## The Anatomy of Effective Context

### System Prompts

System prompts should strike a balance — "specific enough to guide behavior effectively, yet flexible enough to provide the model with strong heuristics." Avoid two extremes:
- Hardcoding complex, brittle logic
- Providing vague, high-level guidance

Use organized sections with XML tagging or Markdown headers. Start with minimal prompts and iterate based on failure modes.

### Tools

Tools must be "self-contained, robust to error, and extremely clear with respect to their intended use." Common failure: bloated tool sets with overlapping functionality or ambiguous decision points.

### Examples

Provide diverse, canonical examples rather than exhaustive edge cases. "For an LLM, examples are the 'pictures' worth a thousand words."

## Context Retrieval and Agentic Search

### Just-in-Time Context Strategies

Rather than pre-loading all data, agents maintain lightweight identifiers (file paths, queries, links) and retrieve data at runtime using tools. Claude Code exemplifies this approach, using targeted queries and Bash commands to analyze large datasets without loading full objects into context.

### Progressive Disclosure

Agents incrementally discover context through exploration. File hierarchies, naming conventions, and timestamps provide signals guiding behavior. This self-managed approach keeps agents focused on relevant subsets.

### Trade-offs

Runtime exploration is slower than pre-computed retrieval but reduces context pollution. Hybrid strategies — retrieving some data upfront while enabling autonomous exploration — balance speed and flexibility.

## Context Engineering for Long-Horizon Tasks

Agents working across extended timeframes (hours to days) need specialized techniques to manage context windows.

### Compaction

Summarize conversations nearing the context limit and restart with compressed summaries. Claude Code preserves architectural decisions and unresolved bugs while discarding redundant tool outputs. The art lies in balancing "recall to ensure your compaction prompt captures every relevant piece of information from the trace, then iterate to improve precision."

### Structured Note-Taking

Agents write notes persisted outside the context window, retrieving them later. Claude playing Pokemon demonstrates this pattern — maintaining tallies across thousands of steps, tracking achievements, and remembering strategies without explicit memory prompts.

Anthropic released a memory tool in public beta on the Claude Developer Platform, enabling file-based knowledge storage across sessions.

### Sub-Agent Architectures

Specialized sub-agents handle focused tasks with clean context windows while a main agent coordinates. Each sub-agent returns 1,000-2,000 token summaries of extensive exploration. This "clear separation of concerns" improved performance on complex research tasks compared to single-agent systems.

## Conclusion

Context engineering represents a fundamental shift in building with LLMs. As capabilities scale, treating "context as a precious, finite resource will remain central to building reliable, effective agents." The guiding principle: "find the smallest set of high-signal tokens that maximize the likelihood of your desired outcome."

Smarter models require less prescriptive engineering, enabling greater autonomy. Yet context remains fundamentally constrained — making thoughtful curation essential regardless of future capability improvements.
