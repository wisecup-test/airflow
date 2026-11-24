---
version: 2
name: cross-cutting-provider-packages-sharing-namespace-pref-4200
description: "pkgutil Namespace Package Extension Protocol: Provider Packages Sharing Namespace Prefix Not"
metadata:
  actual:
    adr_id: 420039a2-bdd3-4d4c-a0c3-c8f699208d83
    status: accepted
    candidate_group_id: 33b401d2-788b-4256-9326-1c1e0373fe57
    evidence:
      source: deep
  scope:
    root: providers/apache/beam
    paths:
      - src/airflow/**
---

# pkgutil Namespace Package Extension Protocol: Provider Packages Sharing Namespace Prefix Not

Scope: applies to `providers/apache/beam/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Shared hierarchical namespace package boundaries across modular provider distributions
- Public API export locations requiring cross-distribution path aggregation

### Rules

- **R-PEA-001** MUST_NOT: Provider packages sharing a namespace prefix MUST NOT define standalone non-extending package roots that shadow peer provider distributions.

### Verify

- Discover and run the project static analysis and linting suite to check namespace package initialization modules.
- Discover and execute cross-provider integration test commands to verify multi-package namespace resolution.

**Accept when:**

- All shared namespace boundary modules successfully extend search paths without shadowing sibling provider packages.
- Integration tests confirm that classes and functions from separate provider packages are importable under the common namespace.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration automated test suites
- Verified by: Static analysis checks verifying namespace boundary initialization protocols
- Verified by: Peer code review for all new and updated provider distributions
- Violation handling: Pull requests omitting namespace extension protocols on shared boundaries are blocked from merging.
- Violation handling: Releases failing multi-package resolution tests are quarantined and rejected.
- Exception process: Exceptions require documented architectural justification approved by the architecture review board.
</enforcement>
