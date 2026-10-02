---
name: "skill-lifecycle-library"
description: "Extracts reusable skills, execution steps, and trigger conditions from successful agent runs; manages versioning and team sharing."
license: MIT
---

# Skill Lifecycle Library

## Overview
This skill captures expert problem-solving workflows executed by agents, converts them into reusable, versioned skills, and enables sharing across individuals and teams.

## Key Capabilities
- **Automated Extraction**: Distills tool-call sequences and successful execution traces into repeatable steps.
- **Specification Structuring**: Formats skills with explicit trigger boundaries, prerequisites, execution recipes, and expected outputs.
- **Lifecycle Governance**: Supports draft, private, team review, published, and deprecated states.
- **Cross-Agent Dispatch**: Matches incoming user tasks with relevant team skills and injects them into the designated agent's context.

## Operational Workflow
1. **Trace Analysis**: Analyze completed multi-step execution logs for novel problem-solving patterns.
2. **Drafting**: Generate structured skill specification with trigger criteria using `skill_manager`.
3. **Verification**: Validate schema compliance, tool dependency availability, and safety constraints.
4. **Publishing**: Assign appropriate visibility (`private`, `team`, `restricted`) and register to the active skill catalog.
