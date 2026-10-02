---
name: "zero-code-proxy-routing"
description: "Proxies agent LLM traffic seamlessly, automatically injecting relevant memory contexts into prompts and intercepting outputs for continuous learning."
license: MIT
---

# Zero-Code Proxy Routing

## Overview
This skill operates a protocol-compliant LLM gateway proxy that intercepts standard OpenAI and Anthropic API traffic from external agents, transparently injecting relevant memory assets and harvesting continuous learnings without agent code changes.

## Key Capabilities
- **Protocol Compatibility**: Fully implements OpenAI `/v1/chat/completions` and Anthropic `/v1/messages` protocols.
- **Dynamic Context Injection**: Enriches system prompts with retrieved memory atoms, relevant skills, and wiki pages on the fly.
- **Streaming Pass-Through**: Preserves Server-Sent Events (SSE) streaming with minimal latency penalty (<50ms).
- **Asynchronous Extraction**: Dispatches conversation logs to background distillation workers without blocking the real-time response stream.

## Operational Workflow
1. **Request Interception**: Receive incoming completion payload from client agent via `proxy_context_injector`.
2. **Context Enrichment**: Retrieve top-$k$ memory assets and prepend to system or user message blocks.
3. **Upstream Forwarding**: Forward enriched payload to target foundation model (OpenAI, DeepSeek, Claude, Hunyuan).
4. **Stream & Asynchronous Harvesting**: Stream response tokens back to agent while queueing turn data for memory distillation.
