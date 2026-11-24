---
version: 2
name: cross-cutting-prior-introducing-updating-dependency-in-b681
description: "Adoption of collections.abc for Collection Protocols and Abstract Container Interfaces: Prior Introducing Updating Dependency Integrations Typing"
metadata:
  actual:
    adr_id: b68197b1-feb9-4221-92cf-7779bd2005f9
    status: accepted
    candidate_group_id: e0b68e13-b0f2-43e5-baa4-7d1575062d00
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/**
---

# Adoption of collections.abc for Collection Protocols and Abstract Container Interfaces: Prior Introducing Updating Dependency Integrations Typing

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/**`.

These rules are ALWAYS ACTIVE for:
- Shared development modules, testing utilities, mock fixtures, and documentation extensions handling collection data structures.
- Public and internal interface definitions that accept or process iterable, sequence, or mapping parameters.

### Rules

- **R-LM-001** MUST: Prior to introducing or updating dependency integrations or typing definitions, engineers MUST inspect the repository dependency manifest and locked resolution artifacts to confirm supported interface contracts.

### Verify

- Discover and run the repository static type checker to verify that collection annotations conform to collections.abc protocols.
- Discover and execute the repository test runner across shared utility and test framework test suites to validate collection compatibility.

**Accept when:**

- Static type analysis across all shared modules completes with zero protocol incompatibility errors.
- All unit and integration test suites utilizing mock containers and collection handlers pass without interface errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration automated static type checking pipelines.
- Verified by: Peer code review verification during pull request inspection.
- Violation handling: Automated pull request check failures blocking merge when concrete container parameters violate abstraction standards.
- Violation handling: Required code refactoring during review before merging into protected branches.
- Exception process: Submit an architectural review request with documented technical rationale explaining why an external boundary requires concrete container coupling.
</enforcement>
