---
version: 2
name: cross-cutting-consumer-inspect-repository-dependency-r-ed5c
description: "Adoption of react-icons/fi for User Interface Iconography: Consumer Inspect Repository Dependency Resolution Lock"
metadata:
  actual:
    adr_id: ed5c7bcd-229f-4718-b826-4824e2644bea
    status: accepted
    candidate_group_id: 15a2a578-19d9-42ce-a6d7-350b5449995e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/**
---

# Adoption of react-icons/fi for User Interface Iconography: Consumer Inspect Repository Dependency Resolution Lock

Scope: applies to `airflow-core/src/airflow/ui/src/**`.

These rules are ALWAYS ACTIVE for:
- User interface layout components requiring visual symbols or navigation cues.
- Action buttons, dropdown indicators, and status badges within frontend pages.

### Rules

- **R-LM-001** MUST: The consumer MUST inspect the repository dependency resolution lock artifact to determine the exact resolved version of react-icons before adding or modifying icon usages.

### Verify

- Discover and run the repository linter to verify that iconography imports conform to authorized submodule paths.
- Discover and execute the frontend type-checking and build validation commands to confirm icon symbol resolutions.

**Accept when:**

- Repository static analysis passes with zero import violations regarding iconography module paths.
- Frontend compilation completes successfully with all icon symbols resolved from authorized submodules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in continuous integration pipelines.
- Verified by: Peer code review for visual consistency and proper subpath import structure.
- Violation handling: Pull requests containing disallowed root imports or unapproved inline SVG markup are blocked until corrected.
- Exception process: Exceptions for unique proprietary graphics or fallback icon subsets must be submitted via design review approval.
</enforcement>
