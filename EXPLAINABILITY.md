# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **TencentDB Agent Memory** (`tencentdb-agent-memory`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** TencentDB Agent Memory (`tencentdb-agent-memory`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Developer Tools / Multi-Agent Memory, Knowledge Graphs & Zero-Code Proxy  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), SOC 2, ISO 27001  

---

## How the Agent Decides

TencentDB Agent Memory operates an intelligent, multi-layer memory ingestion, distillation, and retrieval architecture designed to eliminate redundant context across agent sessions. The agent manages short-term conversational context, mid-term tool execution trajectories, and long-term hierarchical knowledge representations through a deterministic five-stage decision pipeline.

### 1. Decision Architecture

The runtime memory routing, semantic recall, context fitting, and asynchronous knowledge distillation operate across a deterministic, five-stage pipeline:

```
[ Inbound Agent LLM Request / Tool Output Trajectory ]
                      │
                      ▼
[Stage 1: Intent Extraction & PII Scrubbing Gate]
  - Parses caller identity, session tokens, and raw turn payload
  - Runs deterministic regex & NER sanitization against credentials and secrets
  - Dispatches query vectorization to dense embedding pipeline
                      ▼
[Stage 2: Hybrid Retrieval Gate (Vector + Lexical)]
  - Dispatches parallel dense cosine similarity search over vector stores
  - Computes BM25 lexical relevance against symbol and documentation indexes
  - Evaluates temporal decay penalty based on memory access recency
                      ▼
[Stage 3: Cognitive Re-Ranking & Budget Fitting Gate]
  - Computes composite multi-factor relevance score S_memory
  - Enforces strict token limit ceiling (T_budget <= 2000 tokens)
  - Prunes low-relevance items and structures priority injection order
                      ▼
[Stage 4: Dynamic Context Injection & Stream Pass-Through Gate]
  - Injects ranked memory atoms into LLM system prompt context
  - Streams conversational turns with low latency pass-through
  - Captures model response and tool trajectory logs
                      ▼
[Stage 5: Asynchronous Layer Distillation Gate (L0 -> L1 -> L2 -> L3)]
  - Batches raw conversational turns (L0) for offline background processing
  - Synthesizes atomic facts (L1), workflow procedures (L2), and persona profiles (L3)
  - Updates CodeGraph indexes and tenant ACL permissions
                      ▼
[ Continuous Agent Memory State Updated & Persisted ]
```

### 2. Scoring Methodology & Rubric Formulations

When retrieving candidate memory atoms, skills, or CodeGraph snippets for an active agent turn, the engine evaluates two deterministic scoring formulations:

1. **Composite Memory Relevance Score ($S_{\text{memory}}$)**:
   $$S_{\text{memory}} = \alpha \cdot \cos(\vec{q}, \vec{m}) + \beta \cdot \text{BM25}(q, m) + \gamma \cdot \exp\left(-\lambda \cdot \Delta t\right) + \delta \cdot A_{\text{layer}}$$
   where:
   - $\cos(\vec{q}, \vec{m})$: Dense cosine similarity between prompt query vector $\vec{q}$ and stored memory vector $\vec{m}$.
   - $\text{BM25}(q, m) \in [0, 1]$: Normalized lexical BM25 match score across keyword and symbol indices.
   - $\exp\left(-\lambda \cdot \Delta t\right)$: Temporal decay penalty where $\Delta t$ represents days elapsed since last recall/update and decay constant $\lambda = 0.05$.
   - $A_{\text{layer}} \in [0, 1]$: Cognitive layer priority multiplier ($L3 = 1.0, L2 = 0.85, L1 = 0.70$).
   - Standard weightings: $\alpha = 0.45$, $\beta = 0.25$, $\gamma = 0.15$, $\delta = 0.15$ ($\sum = 1.0$).
   - Injection threshold: A memory atom is eligible for injection only if $S_{\text{memory}} \ge \tau_{\text{relevance}} = 0.65$.

2. **Memory Quality & Distillation Health Index ($I_{\text{distill}}$)**:
   $$I_{\text{distill}} = \left(w_f \cdot F_{\text{coherence}}\right) + \left(w_c \cdot C_{\text{compression}}\right) + \left(w_d \cdot (1 - D_{\text{conflict}})\right)$$
   where:
   - $F_{\text{coherence}} \in [0, 1]$: Semantic coherence score between original turns and distilled atomic proposition.
   - $C_{\text{compression}} = \min\left(1.0, \frac{T_{\text{source}} - T_{\text{distilled}}}{T_{\text{source}}}\right)$: Token compression ratio achieved during extraction.
   - $D_{\text{conflict}} \in [0, 1]$: Contradiction index evaluating overlap against existing active knowledge base entries.
   - Weights: $w_f = 0.40$, $w_c = 0.30$, $w_d = 0.30$. Distillation is approved when $I_{\text{distill}} \ge 0.70$.

### 3. Thresholding & Refusal Decision Criteria

TencentDB Agent Memory deterministically refuses requests that violate memory boundaries or integrity thresholds:
- **Refusal on Context Window Exhaustion**: Requests where injected context exceeds the token budget ($T_{\text{budget}} > 2000$ tokens) are pruned to top-$k$ assets with code `ERR_TOKEN_BUDGET_EXCEEDED`.
- **Refusal on Unauthorized Asset Access**: Agent attempts to query `private` or `restricted` assets without verified ACL tenant credentials are deterministically refused with code `ERR_UNAUTHORIZED_ASSET_ACCESS`.
- **Refusal on Sub-Threshold Similarity**: Candidate memory atoms with composite relevance scores below the cutoff ($S_{\text{memory}} < 0.65$) are rejected from injection with code `ERR_LOW_SIMILARITY_SCORE`.
- **Refusal on Unmasked Secrets & PII**: Payloads containing raw unmasked credentials, API keys, or sensitive personal identifiers trigger automated rejection prior to embedding with code `ERR_PII_VIOLATION_DETECTED`.
- **Refusal on Storage Latency Timeout**: Backend vector database lookups exceeding 250ms latency ceiling are rejected from blocking execution with code `ERR_STORAGE_TIMEOUT`.

### 4. Fallback Decision Mechanism

TencentDB Agent Memory implements a resilient multi-tier fallback architecture:
- **Embedded SQLite Vector Fallback**: When remote Tencent Cloud VectorDB or MongoDB instances are unreachable or time out (> 250ms), the system falls back to an embedded local SQLite vector store (`sqlite-vec`).
- **Lexical BM25 Search Fallback**: If dense embedding model inference is unavailable or rate-limited, retrieval degrades gracefully to pure lexical BM25 keyword matching over memory text columns.
- **Fail-Open Pass-Through Fallback**: If the memory subsystem experiences unrecoverable faults, the proxy fails open, directly forwarding requests upstream to maintain uninterrupted agent execution.
- **Model Fallback Cascade**: Hierarchical knowledge extraction and persona distillation default to `gemini-2.0-flash` with automatic failover to `gpt-4o` and `claude-3-5-sonnet`.

### 5. Human-in-the-Loop Governance

TencentDB Agent Memory preserves administrator primacy and human review across all memory operations:
- **Asset Visibility Escalation Approval**: Promoting memory atoms or extracted skills from `private` to `team` or `public` requires explicit approval from the asset owner or administrator.
- **Memory Inspection & Curation**: System operators can inspect, edit, or purge distorted memory atoms and persona summaries via the web-based MemoryPanel.
- **Tenant & Role-Based Access Control**: Administrators oversee organizational workspaces, role assignments (Admin, Member), and cryptographic agent key bindings.

---

## The Data It Uses

### 1. Ingested Input Data

- **Turn-by-Turn Dialog**: Inbound agent conversation turns, system prompts, tool execution records, and user queries.
- **Extracted Code & Documentation**: Source code files, AST symbol trees, architectural Markdown documents, and API runbooks.
- **Agent Action Trajectories**: Tool parameters, exit codes, and intermediate reasoning steps captured during complex multi-step workflows.

### 2. Configuration & Reference Data

- **Structured Knowledge Graph**: CodeGraph symbol indices, call-graph adjacency tables, and Wiki document relationship graphs.
- **Hierarchical Memory Store**: Layered tables storing L1 atomic facts, L2 scenario workflows, and L3 user/agent persona vectors.
- **Tenant ACL Mappings**: User IDs, team IDs, agent keys, and visibility permission matrices (`private`, `team`, `restricted`).

### 3. Base Model & Inference Lineage

- **Embedding Models**: Local `bge-m3`, `text-embedding-3-small`, or Hunyuan Embeddings for dense vector representations.
- **Distillation LLMs**: Dedicated extraction models (DeepSeek-V3, GPT-4o-mini, Hunyuan-Turbo) orchestrating asynchronous L0 -> L1 -> L2 -> L3 distillation.
- **Proxy Gateway**: High-throughput TypeScript/Node.js HTTP/SSE streaming proxy handling concurrent agent connections.

### 4. Data Privacy, Storage, and Retention

- **Strict PII Redaction**: Automatic regex and NER filters scrub access tokens, private keys, credit cards, and personal contact info before storage.
- **Tenant Data Isolation**: Multi-tenant database schemas partition memories by `tenant_id`, `team_id`, and `user_id`.
- **Configurable Retention Windows**: L0 raw conversational logs are automatically aged out after 30 days; distilled L1 atoms and L3 personas persist indefinitely unless explicitly deleted by users.
- **Encryption at Rest & in Transit**: TLS 1.3 encryption across all proxy endpoints; AES-256 encryption for persisted memory tables and vector indices.

---

## Limitations

### 1. Asynchronous Distillation Lag
- **Limitation**: Distillation of raw conversation turns into L1 atoms runs asynchronously, meaning immediate follow-up questions within seconds may rely on short-term context rather than newly distilled memory.
- **Mitigation**: Maintain a transient short-term buffer in Redis/memory cache to bridge immediate turn-level context until background distillation completes.

### 2. Memory Divergence & Stale Facts
- **Limitation**: User preferences or codebase details may change over time, resulting in conflicting historical memory atoms.
- **Mitigation**: Implement automated timestamp-based conflict resolution, where newer verified facts supersede older contradictory records.

### 3. Token Overhead on High-Dimension CodeGraphs
- **Limitation**: Injecting exhaustive call graphs for massive enterprise monoliths into LLM prompts quickly exhausts context windows.
- **Mitigation**: Provide localized BFS impact traversals constrained to immediate callers/callees (max depth 2) with pagination.

### 4. Cross-Model Embedding Incompatibility
- **Limitation**: Switching embedding models (e.g., from OpenAI embeddings to local BGE) invalidates existing vector indices.
- **Mitigation**: Provide automated re-embedding and vector migration CLI scripts (`migrate-sqlite-to-tcvdb`, `export-tencent-vdb`).

### 5. Multi-Agent Skill Overfitting
- **Limitation**: Automatically extracted skills may contain project-specific paths or assumptions that fail when executed by other agents.
- **Mitigation**: Enforce human review or sandbox validation of newly proposed skills before marking them as team-accessible.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Memory relevance scoring & distillation formulas | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & admin oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested dialog turns, code & trajectories | Section 1 | Verified |
| - Configuration, CodeGraph & tenant ACL schemas | Section 2 | Verified |
| - Base model lineage & distillation models | Section 3 | Verified |
| - Data privacy, AES-256 storage & SOC 2/ISO 27001 | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - Asynchronous distillation lag | Section 1 | Verified |
| - Memory divergence & stale facts | Section 2 | Verified |
| - Token overhead on high-dimension CodeGraphs | Section 3 | Verified |
| - Cross-model embedding incompatibility | Section 4 | Verified |
| - Multi-agent skill overfitting | Section 5 | Verified |
