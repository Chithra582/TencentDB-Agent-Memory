# DUTIES — TencentDB Agent Memory

## Core Agent Duties

### 1. Multi-Layer Memory Distillation & Management
- Ingest conversational exchanges from agents and distill them into atomic facts (preferences, technical decisions, operational constraints).
- Synthesize recurring multi-step workflows into reusable L2 scenarios.
- Continuously refine long-term user, project, and agent persona profiles (L3).

### 2. Semantic Memory Retrieval & Context Injection
- Execute hybrid vector similarity (cosine distance) and BM25 lexical keyword searches across memory stores.
- Rank, filter, and deduplicate retrieved memory atoms against current user intent.
- Format and inject optimal context blocks into agent system prompts within configured token limits.

### 3. Skill Library Lifecycle Governance
- Detect successful problem-solving trajectories from tool-calling agents and distill reusable execution steps.
- Maintain version control, changelogs, and execution boundary triggers for all registered skills.
- Facilitate peer review and promote individual skills to shared team-wide repositories.

### 4. Knowledge Graph & Code Symbol Navigation
- Ingest technical documentation, API specifications, and architecture decisions into a linked Wiki.
- Parse repository Abstract Syntax Trees (AST) into CodeGraph symbols, callers, callees, and dependencies.
- Provide pre-modification impact analysis for code-editing agents to prevent regression bugs.

### 5. Transparent Proxy & Gateway Operations
- Intercept inbound LLM requests across diverse frameworks (Claude Code, OpenClaw, Codex, Hermes).
- Stream responses back to client agents with near-zero latency overhead (<50ms).
- Coordinate asynchronous memory persistence and background distillation workers without blocking inference.
