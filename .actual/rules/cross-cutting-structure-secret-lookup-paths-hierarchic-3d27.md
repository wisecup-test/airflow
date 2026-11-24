---
version: 2
name: cross-cutting-structure-secret-lookup-paths-hierarchic-3d27
description: "Akeyless SDK Integration for Centralized Secrets Management: Structure Secret Lookup Paths Hierarchically Support"
metadata:
  actual:
    adr_id: 3d276593-8f79-45e9-b3e2-28c62d573972
    status: accepted
    candidate_group_id: 6699a070-a2b8-4a6c-aeb1-776408472d35
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - docs/**
      - src/airflow/providers/akeyless/**
---

# Akeyless SDK Integration for Centralized Secrets Management: Structure Secret Lookup Paths Hierarchically Support

Scope: applies to `providers/akeyless/docs/**`, `providers/akeyless/src/airflow/providers/akeyless/**`.

These rules are ALWAYS ACTIVE for:
- All secret, variable, and connection retrieval logic interacting with the Akeyless Vault Platform.
- Operational hooks and configuration backends implementing external credential resolution.

### Rules

- **R-SC-001** SHOULD: Structure secret lookup paths hierarchically to support team-scoped prefixes with fallback to global namespaces in multi-team environments.

### Verify

- Discover and run the project test runner for unit and integration suites targeting the secrets backend and hook implementations.
- Execute repository linter and security analysis suites to confirm absence of hardcoded credentials and adherence to secret retrieval abstractions.

**Accept when:**

- Repository test suites validate secret retrieval, connection generation, and token expiration caching against mock vault responses without failures.
- Static analysis and linting confirm zero direct environment credential reads in business logic and no unencrypted secret logging.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated pull request checks executing unit and integration test suites.
- Verified by: Static analysis rules verifying backend instantiation and preventing credential leakage in logs.
- Verified by: Security and architecture peer review during provider modifications.
- Violation handling: Pull requests introducing direct hardcoded secrets or bypassing hook and backend abstractions are blocked from merging.
- Violation handling: Violations detected in runtime configurations trigger security alerts requiring remediation before pipeline activation.
- Exception process: Exceptions require formal security architecture review documenting why external vault retrieval is technically infeasible for a specific component.
- Exception process: Approved exceptions must define an alternative rotation and encryption strategy with designated expiration dates.
</enforcement>
