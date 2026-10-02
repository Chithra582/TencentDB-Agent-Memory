---
name: "knowledge-wiki-codegraph"
description: "Builds linked documentation wikis and call-graph symbol indexes (CodeGraph) for impact analysis before code modifications."
license: MIT
---

# Knowledge Wiki and CodeGraph

## Overview
This skill indexes organizational documentation and software repositories, providing agents with structured conceptual knowledge and deep code symbol dependency graphs.

## Key Capabilities
- **Document Graph (Wiki)**: Parses design documents, architecture specs, and runbooks into markdown pages with bidirectional links.
- **Symbol Indexing (CodeGraph)**: Analyzes source code ASTs to index classes, functions, methods, and variable declarations.
- **Call Relationship Mapping**: Traces caller and callee hierarchies across packages and modules.
- **Impact Analysis**: Assesses downstream ripple effects and dependent modules prior to code modifications.

## Operational Workflow
1. **Repository Ingestion**: Scan code repositories and document directories using `codegraph_indexer`.
2. **AST Parsing**: Extract symbol signatures, docstrings, imports, and cross-references.
3. **Graph Construction**: Build adjacency lists linking callers, callees, definitions, and implementations.
4. **Impact Querying**: Given a target file or symbol, query all dependent components to guide safe refactoring.
