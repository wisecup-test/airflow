---
version: 2
name: cross-cutting-random-module-not-used-generating-5de1
description: "Adoption of Standard random Module for Non-Cryptographic Randomization: Random Module Not Used Generating Cryptographic"
metadata:
  actual:
    adr_id: 5de15802-1997-495b-9ba8-263ee1283807
    status: accepted
    candidate_group_id: 76ddf02b-3ac7-4190-9c41-acd2d0d6811e
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Adoption of Standard random Module for Non-Cryptographic Randomization: Random Module Not Used Generating Cryptographic

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Developer environment automation and build parameter generation modules requiring transient pseudo-randomness
- Non-cryptographic cache invalidation and utility string generation

### Rules

- **R-LM-001** MUST_NOT: The random module MUST_NOT be used for generating cryptographic keys, authentication tokens, security secrets, or sensitive credentials.

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
