---
version: 2
name: cross-cutting-child-entities-within-hierarchical-paren-9ee4
description: "weakref Module Adoption for Cycle-Free Hierarchical Reference Management: Child Entities Within Hierarchical Parent Relationships"
metadata:
  actual:
    adr_id: 9ee4ed96-6bb4-47cc-ab31-7c12ca84b378
    status: accepted
    candidate_group_id: 4e7616c0-aaf6-46f8-85d7-4d2f47aa6208
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# weakref Module Adoption for Cycle-Free Hierarchical Reference Management: Child Entities Within Hierarchical Parent Relationships

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Hierarchical graph data structures with bidirectional parent-child navigation
- Resource accumulators and streaming buffers attached to long-running application runtimes

### Rules

- **R-LM-001** MUST: Child entities within hierarchical parent-child relationships MUST encapsulate references to their parent containers using weakref.proxy or weakref.ref instead of direct strong references.

### Verify

- Discover the project test execution runner from repository configuration and execute the unit test suite covering graph serialization and reference cycle deallocation.
- Discover the repository static analysis linter and run validation across core modules to verify compliance with memory and reference lifecycle conventions.

**Accept when:**

- All graph traversal and parent resolution tests pass without triggering reference exceptions on weakly referenced parent objects.
- Object graphs with bidirectional parent-child links release memory upon parent dereferencing without requiring explicit cyclic garbage collection sweeps.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites validating memory deallocation and parent resolution
- Verified by: Peer code reviews auditing back-reference implementations in structural components
- Violation handling: Pull requests introducing strong circular references in hierarchical structures will be blocked during review
- Violation handling: Memory accumulation detections during stress testing require refactoring to weak references before release
- Exception process: Exceptions require documented architecture review demonstrating that lifecycle boundaries guarantee simultaneous parent-child destruction without cycles.
</enforcement>
