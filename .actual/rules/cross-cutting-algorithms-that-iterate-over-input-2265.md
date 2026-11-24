---
version: 2
name: cross-cutting-algorithms-that-iterate-over-input-2265
description: "Adoption of collections.abc for Container and Callable Interface Protocols: Algorithms That Iterate Over Input Collections"
metadata:
  actual:
    adr_id: 226508a2-2a3d-4c13-8080-0ad6feb005d9
    status: accepted
    candidate_group_id: 52ea4356-a278-4a3f-83da-2c5b4a6792a7
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# Adoption of collections.abc for Container and Callable Interface Protocols: Algorithms That Iterate Over Input Collections

Scope: applies to `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- Public interface contracts, helper utilities, and formatting routines that accept or return collection types or callable handlers.
- Cross-module boundaries connecting CLI argument parsers to console formatters and execution commands.

### Rules

- **R-LM-001** SHOULD: Algorithms that iterate over input collections once without requiring random access SHOULD accept Iterable from collections.abc to support generator and stream evaluation.

### Verify

- Discover and execute the repository static analysis and type checking suite to verify protocol compliance.
- Discover and execute the repository test suite to confirm polymorphic collection handling across formatters and parsers.

**Accept when:**

- All collection-consuming public interfaces declare collections.abc protocols and pass static contract verification without type violations.
- Formatting utilities and command parsing routines pass all automated test suites when handling conforming sequence and callable inputs.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis and type verification executed in continuous integration pipelines.
- Verified by: Peer code reviews for all cross-module contract changes and public function signatures.
- Violation handling: Code review approvals are withheld and pull requests are blocked if new interface boundaries specify concrete container types where collections.abc protocols suffice.
- Exception process: Exceptions require documented justification during code review demonstrating why an abstract collection protocol cannot satisfy the mutation or operational requirement.
</enforcement>
