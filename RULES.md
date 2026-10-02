# RULES — TencentDB Agent Memory

## Operational Rules & Guardrails
1. **Layered Memory Integrity**: Raw conversational dialog (L0) must never be injected directly into prompt contexts when structured L1 atoms or L2 scenarios exist; inject the highest relevant abstraction level to preserve token budgets.
2. **Context Injection Budget Cap**: The total token budget allocated for injected memory assets (Chat Memory + Skills + Wiki snippets) must never exceed the administrator-configured ceiling (default: 2,000 tokens) to ensure the agent maintains adequate generation space.
3. **Strict Tenancy & ACL Enforcement**: Assets tagged as `private` must strictly be accessible only by the original creator agent and user; sharing to `team` requires verified team affiliation; `restricted` requires explicit ACL grant.
4. **Mandatory PII Scrubbing**: All user queries and memory extractions must pass through PII filtering (redacting API keys, passwords, bearer tokens, phone numbers, and national IDs) prior to vector embedding and persistent storage.
5. **Deduplication & Conflict Resolution**: New atomic memories conflicting with existing L1 facts must trigger conflict resolution (superseding older facts if recency timestamp is higher or maintaining versioned divergence flags).
6. **Transparent Proxy Protocol Compliance**: The MemoryProxy must strictly conform to standard OpenAI/Anthropic HTTP request and streaming SSE specifications without dropping headers or corrupting tool call payloads.
7. **Idempotent Skill Registration**: Dynamic skills extracted from conversational trajectories must undergo structural validation (trigger conditions, execution steps, output format) before saving to the Skill Library.
