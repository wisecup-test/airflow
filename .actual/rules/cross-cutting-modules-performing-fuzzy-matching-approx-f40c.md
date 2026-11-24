---
version: 2
name: cross-cutting-modules-performing-fuzzy-matching-approx-f40c
description: "difflib Module Adoption for Sequence Matching and Delta Computation: Modules Performing Fuzzy Matching Approximate String"
metadata:
  actual:
    adr_id: f40c183a-2a74-4f57-9f5c-72bb76f0bffd
    status: accepted
    candidate_group_id: f15776ee-5877-4e35-a99a-fe4aacc3034a
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# difflib Module Adoption for Sequence Matching and Delta Computation: Modules Performing Fuzzy Matching Approximate String

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Developer tooling, release preparation scripts, and test target selection utilities requiring text difference computation or approximate sequence matching.

### Rules

- **R-LM-001** SHOULD: Modules performing fuzzy matching or approximate string selection SHOULD utilize difflib SequenceMatcher or get_close_matches to resolve ambiguous configuration identifiers.

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
