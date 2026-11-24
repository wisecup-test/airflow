---
version: 2
name: cross-cutting-modules-requiring-pseudo-random-values-14db
description: "Adoption of Standard random Module for Non-Cryptographic Randomization: Modules Requiring Pseudo Random Values Non"
metadata:
  actual:
    adr_id: 14db7f2f-1b48-483b-998b-a5c67b8c4a5f
    status: accepted
    candidate_group_id: 76ddf02b-3ac7-4190-9c41-acd2d0d6811e
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Adoption of Standard random Module for Non-Cryptographic Randomization: Modules Requiring Pseudo Random Values Non

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Developer environment automation and build parameter generation modules requiring transient pseudo-randomness
- Non-cryptographic cache invalidation and utility string generation

### Rules

- **R-LM-001** MUST: Modules requiring pseudo-random values for non-cryptographic automation tasks MUST use the standard random module and its pseudorandom generation APIs.

### Verify

- Discover and execute the project static analysis validation target to verify module import safety across development tooling.
- Discover and run the test suite covering build parameter generation and utility functions.

**Accept when:**

- Static analysis validates that pseudo-random generation APIs are confined to non-cryptographic modules.
- All test suites covering build parameter generation pass without non-deterministic failures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated code analysis and linting in continuous integration
- Verified by: Peer code review for changes affecting developer tooling and build parameter construction
- Violation handling: Pull requests introducing pseudo-random generation in security contexts are blocked from merging.
- Violation handling: Violations identified in code reviews must replace pseudo-random generation with appropriate cryptographic or deterministic alternatives.
- Exception process: Exceptions require documented justification detailing why non-standard randomization is necessary, approved by the architecture review team.
</enforcement>
