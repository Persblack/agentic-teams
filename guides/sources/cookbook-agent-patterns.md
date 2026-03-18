# Anthropic Cookbook — Agent Patterns

> Source: https://github.com/anthropics/anthropic-cookbook/tree/main/patterns/agents

Reference implementation for [Building Effective Agents](https://anthropic.com/research/building-effective-agents) by Erik Schluntz and Barry Zhang.

## Repository Contents

```
patterns/agents/
├── prompts/                      (prompt templates)
├── README.md
├── basic_workflows.ipynb         (prompt chaining, routing, parallelization)
├── evaluator_optimizer.ipynb     (evaluator-optimizer workflow)
├── orchestrator_workers.ipynb    (orchestrator-workers workflow)
└── util.py                       (utility functions)
```

## Agent Workflows Covered

### Basic Building Blocks (`basic_workflows.ipynb`)
- **Prompt Chaining** — Sequential prompts where output of one feeds into the next
- **Routing** — Directing tasks to appropriate handlers based on input classification
- **Multi-LLM Parallelization** — Running multiple LLM calls concurrently for efficiency

### Advanced Workflows
- **Orchestrator-Subagents** (`orchestrator_workers.ipynb`) — A central orchestrator that delegates work to specialized subagents
- **Evaluator-Optimizer** (`evaluator_optimizer.ipynb`) — An iterative pattern where an evaluator assesses work and an optimizer improves it

## Getting Started

All examples are minimal reference implementations designed to demonstrate practical agent patterns using Claude. The notebooks are runnable Jupyter notebooks with the Anthropic SDK.

The `prompts/` directory contains prompt templates used in the examples, including `research_lead_agent.md` for multi-agent research orchestration.
