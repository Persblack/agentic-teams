# How We Built Our Multi-Agent Research System

> Source: https://www.anthropic.com/engineering/multi-agent-research-system
> Published: Jun 13, 2025

## Overview

Anthropic's Research feature employs multiple Claude agents working together to explore complex topics more effectively. The engineering journey from prototype to production revealed critical lessons about system architecture, tool design, and prompt engineering.

## Benefits of Multi-Agent Systems

### Why Multi-Agents Excel at Research

Research involves open-ended problems where predicting required steps proves difficult. Traditional linear pipelines cannot handle the dynamic, path-dependent nature of investigation. Multi-agent architectures shine because they:

- **Enable parallel exploration:** Subagents operate simultaneously with separate context windows, exploring different question aspects before condensing findings for the lead agent
- **Reduce path dependency:** Distinct tools, prompts, and trajectories enable thorough independent investigations
- **Scale collective intelligence:** Just as human societies became exponentially more capable through coordination, agent groups accomplish more than individuals

### Performance Evidence

Internal evaluations showed a multi-agent system with Claude Opus 4 as lead agent and Claude Sonnet 4 subagents outperformed single-agent Claude Opus 4 by 90.2% on research tasks. When tasked with identifying all board members of Information Technology S&P 500 companies, the multi-agent approach succeeded through task decomposition while sequential searching failed.

### Token Economics

Three factors explained 95% of performance variance in the BrowseComp evaluation:
- Token usage (80% of variance)
- Number of tool calls
- Model choice

Multi-agent systems require significantly more tokens—agents use roughly 4x more tokens than chat, and multi-agent systems use about 15x more. This approach only works economically for high-value tasks with substantial parallelization potential.

### When Multi-Agents Don't Fit

Multi-agent systems struggle with domains requiring shared context or heavy interdependencies. Coding tasks involve fewer parallelizable components, and current LLM agents lack real-time coordination capabilities.

## Architecture Overview

The Research system uses an **orchestrator-worker pattern** where a lead agent coordinates while delegating to specialized subagents operating in parallel.

### Workflow Process

1. User submits query
2. Lead agent analyzes request and develops strategy
3. Lead agent spawns subagents to explore different aspects simultaneously
4. Subagents iteratively use search tools to gather information
5. Subagents return filtered findings to lead agent
6. Lead agent compiles comprehensive answer
7. Citation agent attributes all claims to sources
8. Final results with citations return to user

### Contrast with Traditional RAG

Traditional Retrieval Augmented Generation uses static retrieval—fetching similar chunks upfront. The multi-agent approach dynamically finds relevant information, adapts to new findings, and analyzes results iteratively.

## Prompt Engineering Principles

Managing multi-agent coordination complexity required careful prompt engineering. Early agents made critical errors: spawning 50 subagents for simple queries, endlessly searching for nonexistent sources, and overwhelming each other with updates.

### Eight Key Principles

**1. Think Like Your Agents**
Develop accurate mental models by using Anthropic's Console with identical prompts and tools, observing agent behavior step-by-step. This immediately revealed failure modes: continuing despite sufficient results, verbose queries, and tool misselection.

**2. Teach Orchestration**
Lead agents must decompose queries into specific subtasks with clear objectives, output formats, tool guidance, and boundaries. Vague instructions like "research the semiconductor shortage" caused subagent duplication. Explicit task division prevents inefficiency.

**3. Scale Effort Appropriately**
Embed scaling rules: simple fact-finding needs one agent with 3-10 tool calls; comparisons require 2-4 subagents with 10-15 calls each; complex research uses 10+ subagents with divided responsibilities.

**4. Prioritize Tool Design**
Agent-tool interfaces prove as critical as human-computer interfaces. Bad descriptions send agents down wrong paths entirely. Each tool needs distinct purpose and clarity. With Model Context Protocol servers introducing unseen tools with varying quality descriptions, explicit heuristics matter: examine available tools first, match usage to intent, prefer specialized over generic tools.

**5. Enable Self-Improvement**
Claude 4 models excel as prompt engineers. When given failure modes, they diagnose issues and suggest improvements. A dedicated tool-testing agent found that rewriting poor tool descriptions decreased subsequent task completion by 40%.

**6. Start Wide, Then Narrow**
Mirror expert human research. Agents default to overly specific queries returning few results. Prompting for short, broad queries followed by progressive narrowing improves outcomes.

**7. Guide Thinking Processes**
Extended thinking mode provides a controllable scratchpad. Lead agents use thinking to plan approaches, assess tool fit, determine complexity, and define subagent roles. Testing confirmed extended thinking improved instruction-following, reasoning, and efficiency. Subagents use interleaved thinking after tool results to evaluate quality and refine queries.

**8. Parallelize Tool Calling**
Early sequential searches proved painfully slow. Parallelization changes: lead agents spin up 3-5 subagents in parallel; subagents use 3+ tools in parallel. This reduced research time by up to 90% for complex queries.

### Overall Strategy

Focus on instilling good heuristics rather than rigid rules. Study how skilled humans approach research—decomposing questions, evaluating source quality, adapting approaches, balancing depth versus breadth—and encode these strategies. Set explicit guardrails preventing spiraling behavior and maintain fast iteration loops with observability.

## Evaluation Approaches

Evaluating multi-agent systems presents unique challenges. Agents take different valid paths to identical goals, making traditional step-validation ineffective.

### Start Small and Early

Early development showed dramatic impacts from prompt tweaks (30% to 80% success rates). Testing just 20 queries representing real usage patterns revealed change impacts clearly. Many teams delay evaluation assuming only large-scale testing with hundreds of cases helps—resist this. Small-scale testing immediately uncovers issues.

### LLM-as-Judge Evaluation

Research outputs resist programmatic evaluation due to free-form nature and multiple correct answers. LLM judges evaluate against rubric criteria:
- Factual accuracy
- Citation accuracy
- Completeness
- Source quality
- Tool efficiency

A single LLM call with one prompt outputting 0.0-1.0 scores and pass-fail grades proved most consistent with human judgment. This scaled evaluation to hundreds of outputs.

### Human Evaluation Remains Essential

Automated testing misses edge cases: hallucinated answers on unusual queries, system failures, and subtle biases. Human testers noticed agents consistently chose SEO-optimized content farms over authoritative academic PDFs or blogs. Adding source quality heuristics resolved this.

### Emergent Behaviors Require Framework Thinking

Small lead agent changes unpredictably affect subagent behavior. Best prompts aren't strict instructions but collaboration frameworks defining labor division, problem-solving approaches, and effort budgets. Success requires careful prompting, tool design, solid heuristics, observability, and tight feedback loops.

## Production Reliability Challenges

### Stateful Execution and Error Compounding

Agents run for extended periods maintaining state across many tool calls. Minor system failures become catastrophic without effective mitigations. Rather than restarting from beginning (expensive and frustrating), systems must resume from error points. Combining Claude's intelligence with deterministic safeguards—retry logic and checkpoints—handles issues gracefully.

### Debugging Non-Deterministic Systems

Agents make dynamic decisions, varying between runs despite identical prompts. Users reported agents "not finding obvious information" without visible failure causes. Full production tracing diagnosed root causes systematically. High-level observability monitoring agent decision patterns and interaction structures—without monitoring conversation contents to maintain privacy—revealed unexpected behaviors.

### Deployment Coordination

Agent systems form highly stateful webs of prompts, tools, and execution logic running continuously. Updates risk breaking agents mid-process. Rather than simultaneous version updates, rainbow deployments gradually shift traffic between old and new versions while keeping both running.

### Synchronous Execution Bottlenecks

Currently, lead agents execute subagents synchronously, waiting for completion before proceeding. This simplifies coordination but creates bottlenecks preventing lead agents from steering subagents or enabling subagent coordination. Asynchronous execution enabling concurrent work and dynamic subagent creation would improve parallelism but introduces result coordination, state consistency, and error propagation complexities.

## Conclusion

The gap between prototype and production proves wider than anticipated. Prototype codebases require substantial engineering for reliability. Agentic systems' compound error nature means minor issues derail agents entirely—one failing step cascades into completely different exploration trajectories.

Despite challenges, multi-agent research systems demonstrate proven value. Users report finding unconsidered business opportunities, navigating complex healthcare decisions, resolving technical problems, and saving days of work uncovering research connections.

## Common Use Cases

A Clio embedding plot shows primary Research feature uses:
- Developing software systems across specialized domains (10%)
- Developing and optimizing professional content (8%)
- Business growth and revenue strategies (8%)
- Academic research and educational development (7%)
- Information research and verification about people, places, organizations (5%)

## Appendix: Additional Tips

**End-State Evaluation for State-Mutating Agents**
Focus on end-state evaluation rather than turn-by-turn analysis. Evaluate whether agents achieved correct final states, acknowledging alternative valid paths. Break complex workflows into discrete checkpoints where specific state changes should occur.

**Long-Horizon Conversation Management**
Implement patterns where agents summarize completed phases and store information externally before new tasks. When approaching limits, spawn fresh subagents with clean contexts while maintaining continuity through careful handoffs. Retrieve stored context like research plans from memory rather than losing previous work.

**Subagent Output to Filesystem**
Direct subagent outputs can bypass main coordinators for certain results, improving fidelity and performance. Implement artifact systems where specialized agents create independently persisting outputs. Subagents store work in external systems and pass lightweight references to coordinators. This prevents information loss during multi-stage processing and reduces token overhead from large output copying. Particularly effective for structured outputs like code, reports, or visualizations.
