---
name: "hierarchical-chat-memory"
description: "Ingests multi-turn conversational dialogs and structures memory across 4 cognitive layers (L0 Conversation, L1 Atom, L2 Scenario, L3 Persona)."
license: MIT
---

# Hierarchical Chat Memory

## Overview
This skill implements the 4-layer cognitive memory architecture that transforms unstructured conversational exchanges into structured, reusable knowledge assets for agents.

## Key Capabilities
- **L0 Ingestion**: Captures raw turn-by-turn agent and user messages with accurate temporal timestamps.
- **L1 Atomic Extraction**: Identifies and extracts concrete user preferences, technical constraints, facts, and decisions.
- **L2 Scenario Clustering**: Groups related atomic facts into cohesive situational contexts and task patterns.
- **L3 Persona Synthesis**: Maintains durable profiles capturing long-term user traits, project guidelines, and agent operational styles.

## Operational Workflow
1. **Dialog Ingestion**: Receive conversation batch from active agent session.
2. **Atomic Parsing**: Extract declarative facts, preferences, and project rules via `memory_layer_distiller`.
3. **Hierarchy Synthesis**: Associate extracted atoms with matching L2 scenarios or create new scenario clusters.
4. **Persona Update**: Refine L3 persona vector representations and persist to database.
