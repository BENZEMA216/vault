# Awesome Harness Engineering

> Source: https://github.com/walkinglabs/awesome-harness-engineering
> Ingested: 2026-04-03

A curated list of articles, playbooks, benchmarks, specifications, and open-source projects for harness engineering: the practice of shaping the environment around AI agents so they can work reliably.

Harness engineering sits at the intersection of context engineering, evaluation, observability, orchestration, safe autonomy, and software architecture.

## Foundations

- **Harness engineering: leveraging Codex in an agent-first world** (OpenAI) — Field report on building large applications with Codex using architectural constraints, repo-local instructions, browser validation, and telemetry.
- **Effective harnesses for long-running agents** (Anthropic) — Core article on initializer agents, feature lists, `init.sh`, self-verification, and handoff artifacts across many context windows.
- **Harness design for long-running application development** (Anthropic) — Follow-up focused on improving long-running app generation with better task state and evaluator design.
- **The Anatomy of an Agent Harness** (LangChain) — Agent = Model + Harness. Harness includes prompts, tools, middleware, orchestration, and runtime infrastructure.
- **Harness Engineering** (Thoughtworks/Martin Fowler) — Framing harness work into context engineering, architectural constraints, and entropy management.
- **Building effective agents** (Anthropic) — Guide to workflows, agents, tools, and when structured systems outperform raw prompting.
- **Skill Issue: Harness Engineering for Coding Agents** (HumanLayer) — Practical argument that weak results from coding agents are often harness problems rather than model problems.
- **Your Agent Needs a Harness, Not a Framework** (Inngest) — Case for treating state, retries, traces, and concurrency as first-class infrastructure.

## Context, Memory & Working State

- **Effective context engineering for AI agents** (Anthropic) — Managing the context window as a working memory budget.
- **Context Engineering for AI Agents: Lessons from Building Manus** — KV-cache locality, tool masking, filesystem memory, keeping useful failures in-context.
- **Context Engineering for Coding Agents** (Thoughtworks) — Shaping the task environment so coding agents stay grounded.
- **Advanced Context Engineering for Coding Agents** (HumanLayer) — Reducing context drift and making coding sessions easier to resume.
- **Context-Efficient Backpressure for Coding Agents** (HumanLayer) — Preventing agents from burning context on noisy or low-value work.
- **OpenHands Context Condensation** — Bounded conversation memory preserving goals, progress, critical files, failing tests.
- **Writing a good CLAUDE.md** (HumanLayer) — Practical guide to durable, repo-local instructions.

## Constraints, Guardrails & Safe Autonomy

- **Beyond permission prompts** (Anthropic) — Reducing approval friction without losing control through better sandboxing and policy design.
- **Code execution with MCP** (Anthropic) — Controlled execution power through explicit, inspectable tool boundaries.
- **Writing effective tools for agents** (Anthropic) — Tool interfaces easier for models to call correctly and safely.
- **Mitigating Prompt Injection Attacks** (OpenHands) — Confirmation mode, analyzers, sandboxing, hard policies.
- **Assessing internal quality while coding with an agent** (Thoughtworks) — Moving quality checks into the loop.
- **Anchoring AI to a reference application** (Thoughtworks) — Constraining agents with concrete exemplars.
- **Claude Code: Best practices for agentic coding** (Anthropic) — Repo structure, checkpoints, validation, delegation.

## Specs, Agent Files & Workflow Design

- **AGENTS.md** — Lightweight open format for repo-local agent instructions.
- **agent.md** — Standardization for machine-readable agent instructions.
- **GitHub Spec Kit** — Toolkit for spec-driven development.
- **12 Factor Agents** (HumanLayer) — Operating principles including explicit prompts, state ownership, pause-resume.
- **12-Factor AgentOps** — Operations-oriented companion focused on context discipline and reproducibility.

## Evals & Observability

- **Testing Agent Skills Systematically with Evals** (OpenAI)
- **Agent evals** (OpenAI) — Measuring agent quality with reproducible evaluations.
- **Demystifying Evals for AI Agents** (Anthropic) — What to measure with many possible trajectories.
- **Quantifying infrastructure noise in agentic coding evals** (Anthropic)
- **Improving Deep Agents with harness engineering** (LangChain) — Harness changes alone significantly improve benchmark performance.
- **Learning to Verify AI-Generated Code** (OpenHands) — Layered verification stack using trajectory critics.

## Benchmarks

40+ benchmarks listed including: SWE-bench Verified, Terminal-Bench, OSWorld, WebArena, AgentBench, GAIA, τ-Bench, AppWorld, BrowseComp, MCPMark, and many more. Useful for comparing harness quality, not just model quality.

## Runtimes, Harnesses & Reference Implementations

- **deepagents** (LangChain) — Building deeper, longer-running agents with middleware and harness patterns.
- **SWE-agent** — Mature research coding agent with inspectable harness.
- **Harbor** — Generalized harness for evaluating and improving agents at scale.
- **AgentKit** (Inngest) — TypeScript toolkit for durable, workflow-aware agents.
