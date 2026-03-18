# Building Effective AI Agents

> Source: https://www.anthropic.com/research/building-effective-agents

## Introduction

Anthropic has worked extensively with teams constructing LLM agents across various industries. Their research reveals a consistent pattern: "the most successful implementations weren't using complex frameworks or specialized libraries. Instead, they were building with simple, composable patterns."

## What Are Agents?

The article distinguishes between two fundamental architectural approaches:

**Workflows** represent systems where "LLMs and tools are orchestrated through predefined code paths." These offer predictability for well-defined tasks.

**Agents** are systems where "LLMs dynamically direct their own processes and tool usage, maintaining control over how they accomplish tasks." These excel when flexibility and model-driven decision-making matter.

## When to Build Agentic Systems

The guidance is pragmatic: start simple. "When building applications with LLMs, we recommend finding the simplest solution possible, and only increasing complexity when needed." For many use cases, optimizing individual LLM calls with retrieval and examples suffices. Agentic systems trade latency and cost for improved performance—a tradeoff worth making only when simpler solutions fall short.

## Framework Considerations

While frameworks like Claude Agent SDK, Strands Agents SDK, Rivet, and Vellum streamline development, they introduce abstraction layers that can obscure underlying mechanics. The recommendation: "start by using LLM APIs directly: many patterns can be implemented in a few lines of code."

## Core Patterns

### The Augmented LLM

The foundation combines an LLM with retrieval, tools, and memory capabilities. The Model Context Protocol offers one standardized approach for integrating third-party tools.

### Prompt Chaining

Decompose tasks into sequential steps where each LLM call processes previous outputs. This trades latency for accuracy on complex tasks like marketing copy generation followed by translation.

### Routing

Classify inputs and direct them to specialized downstream handlers. Useful for customer service queries routed by type, or routing simple questions to efficient models and complex ones to capable models.

### Parallelization

Execute multiple LLM calls simultaneously through:
- **Sectioning**: independent subtasks run in parallel
- **Voting**: same task executed multiple times for consensus

Example applications include parallel content moderation checks and security code reviews.

### Orchestrator-Workers

A central LLM decomposes tasks dynamically and delegates to worker LLMs, then synthesizes results. Ideal for unpredictable problem spaces like multi-file code modifications.

### Evaluator-Optimizer

One LLM generates responses while another provides iterative feedback. Particularly effective for literary translation and complex research requiring multiple analysis rounds.

### Autonomous Agents

Agents operate independently on open-ended problems after receiving initial instructions. They require "ground truth" from environmental feedback at each step and must include stopping conditions. While powerful, they demand extensive testing in sandboxed environments with appropriate guardrails due to higher costs and compounding error potential.

## Key Implementation Principles

The article emphasizes three core principles:

1. **Simplicity** in agent design
2. **Transparency** through explicitly shown planning steps
3. **Documentation and testing** of agent-computer interfaces (ACI), treated as seriously as human-computer interface (HCI) design

## Practical Applications

**Customer Support**: Natural fit for agent architectures because interactions combine conversation with action, tools pull customer data, actions are programmable, and success metrics are clear.

**Coding Agents**: Particularly effective because solutions are automatically testable, agents iterate using test feedback, the problem space is well-structured, and output quality is objectively measurable. Anthropic's agents now solve real GitHub issues in the SWE-bench Verified benchmark.

## Tool Design Best Practices

Tool specifications deserve as much attention as overall prompts. Key recommendations include:

- Provide sufficient tokens for model deliberation
- Keep formats close to naturally occurring internet text
- Eliminate formatting overhead like maintaining accurate line counts
- Include examples, edge cases, and clear boundaries in documentation
- Test extensively with diverse inputs
- Apply "poka-yoke" principles making mistakes harder to make

During SWE-bench development, Anthropic found they spent more time optimizing tools than overall prompts. A critical fix involved requiring absolute filepaths instead of relative ones, eliminating errors entirely.

## Conclusion

"Success in the LLM space isn't about building the most sophisticated system. It's about building the right system for your needs." Start with simple prompts, evaluate comprehensively, and add complexity only when demonstrably necessary.
