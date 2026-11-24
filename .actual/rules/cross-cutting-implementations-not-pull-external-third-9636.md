---
version: 2
name: cross-cutting-implementations-not-pull-external-third-9636
description: "difflib Module Adoption for Sequence Matching and Delta Computation: Implementations Not Pull External Third Party"
metadata:
  actual:
    adr_id: 96364b9b-8410-4dd5-97ac-7340a0014bc3
    status: accepted
    candidate_group_id: f15776ee-5877-4e35-a99a-fe4aacc3034a
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# difflib Module Adoption for Sequence Matching and Delta Computation: Implementations Not Pull External Third Party

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Developer tooling, release preparation scripts, and test target selection utilities requiring text difference computation or approximate sequence matching.

### Rules

- **R-LM-001** MUST_NOT: Implementations MUST NOT pull in external third-party fuzzy matching or sequence comparison dependencies unless performance profiling demonstrates algorithmic limitations with difflib for large payloads.

### Verify

- Discover and run the project static analysis and test suite targets to ensure diff generation and sequence matching routines execute without errors.
- Discover and execute the dependency audit script to confirm no extraneous third-party diffing packages are declared in dependency manifests.

**Accept when:**

- All unit tests exercising sequence comparison, change classification, and diff output pass cleanly.
- Static analysis validates that no external third-party sequence matching dependencies are imported in developer tooling modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks running linting and import rule verification.
- Verified by: Peer review on changes touching developer tooling and release automation modules.
- Violation handling: Continuous integration builds fail if unauthorized third-party diffing dependencies are introduced.
- Violation handling: Code review blocks merge until standard library difflib modules replace redundant external packages.
- Exception process: Submit an architectural review request with comparative benchmark evidence demonstrating that difflib cannot meet documented throughput requirements.
</enforcement>
