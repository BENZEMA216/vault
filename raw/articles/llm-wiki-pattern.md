# LLM Wiki Pattern — Karpathy

> Source: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
> Ingested: 2026-04-06

A pattern for building personal knowledge bases using LLMs.

## Core Idea

Instead of RAG (rediscovering knowledge from scratch on every question), the LLM **incrementally builds and maintains a persistent wiki** — a structured, interlinked collection of markdown files. Knowledge is compiled once and kept current, not re-derived on every query. The wiki is a persistent, compounding artifact.

## Three-Layer Architecture

1. **Raw sources** — immutable curated documents (articles, papers, images). LLM reads but never modifies.
2. **The wiki** — LLM-generated markdown files. Summaries, entity pages, concept pages, comparisons, synthesis. LLM owns this entirely.
3. **The schema** — CLAUDE.md/AGENTS.md that defines wiki structure, conventions, and workflows.

## Operations

- **Ingest**: Read source → discuss takeaways → write summary → update index → update related pages → append to log. Single source might touch 10-15 wiki pages.
- **Query**: Search relevant pages → synthesize answer → file valuable results back into wiki as new pages.
- **Lint**: Health-check for contradictions, stale claims, orphan pages, missing cross-references, data gaps.

## Indexing and Logging

- **index.md**: Content-oriented catalog organized by category. LLM reads it first to find relevant pages.
- **log.md**: Append-only chronological record. Parseable with `grep "^## \[" log.md | tail -5`.

## Why This Works

The tedious part is bookkeeping — cross-references, keeping summaries current, noting contradictions. LLMs don't get bored and can touch 15 files in one pass. Maintenance cost → near zero.

Human's job: curate sources, direct analysis, ask good questions.
LLM's job: everything else.

Related to Vannevar Bush's Memex (1945) — personal, curated knowledge store with associative trails.
