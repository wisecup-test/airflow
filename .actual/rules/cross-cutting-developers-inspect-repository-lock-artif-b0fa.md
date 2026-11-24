---
version: 2
name: cross-cutting-developers-inspect-repository-lock-artif-b0fa
description: "collections.abc Interface and Container Protocol Adoption: Developers Inspect Repository Lock Artifact Verify"
metadata:
  actual:
    adr_id: b0fabaa8-c137-4909-8805-8475f7b2b011
    status: accepted
    candidate_group_id: 13aa5576-65d0-44ed-826a-b8ce18720c76
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/utils/**
      - mypy/src/airflow_mypy/plugins/**
---

# collections.abc Interface and Container Protocol Adoption: Developers Inspect Repository Lock Artifact Verify

Scope: applies to `dev/breeze/src/airflow_breeze/utils/**`, `dev/mypy/src/airflow_mypy/plugins/**`.

These rules are ALWAYS ACTIVE for:
- Public and internal utility interfaces defining container or callable parameters.
- Plugin hooks, custom argument converters, and execution wrappers handling multi-element collections.

### Rules

- **R-LM-001** MUST: Developers MUST inspect the repository lock artifact and verify runtime dependency constraints before introducing or modifying collection interfaces.

### Verify

- Discover the project verification script from the repository manifest and execute static analysis across utility modules.
- Run the repository type verification suite to validate conformance to abstract container protocols.

**Accept when:**

- Static type verification completes with zero type mismatch errors on collection signatures.
- All public utility interfaces consume abstract container protocols from collections.abc.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static type analysis in the continuous integration pipeline.
- Verified by: Peer review verification during pull request reviews.
- Violation handling: Pull requests introducing concrete container coupling in public interface signatures fail automated review checks.
- Violation handling: Developers must refactor non-conforming parameters to use appropriate abstract collection protocols.
- Exception process: Submit an exception request detailing specific container performance requirements to the architecture review team.
- Exception process: Document approved exceptions inline with reference to the approved exception tracking ticket.
</enforcement>
