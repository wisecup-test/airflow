---
version: 2
name: cross-cutting-consumer-inspect-repository-dependency-m-f8b8
description: "pkgutil Public Namespace Protocol: Consumer Inspect Repository Dependency Manifest Lock"
metadata:
  actual:
    adr_id: f8b83c94-c7ba-4665-ac8d-c5172269e64a
    status: accepted
    candidate_group_id: ca65f566-bc3e-4a51-b004-c1ca2bef6bd1
    evidence:
      source: deep
  scope:
    root: providers/apache/flink
    paths:
      - src/airflow/**
---

# pkgutil Public Namespace Protocol: Consumer Inspect Repository Dependency Manifest Lock

Scope: applies to `providers/apache/flink/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Intermediate package initialization modules across distributed provider packages that share common root namespace paths
- Public API boundary modules establishing multi-distribution namespace integration

### Rules

- **R-PEA-001** MUST: The consumer MUST inspect the repository dependency manifest and lock resolution artifact to identify the exact resolved version of all runtime dependencies prior to implementing namespace protocols.

### Verify

- Discover the project verification script from the repository manifest and execute the namespace structural lint suite.
- Execute repository test runners to confirm all distributed submodules resolve under the shared parent namespace.

**Accept when:**

- All intermediate namespace initialization modules contain the required `__import__("pkgutil")` protocol statement.
- Distributed provider modules import successfully under the shared namespace hierarchy without module shadowing.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated structural checks in continuous integration pipelines validating namespace initialization declarations
- Verified by: Pull request review verifying new provider distributions follow namespace packaging conventions
- Violation handling: Pull requests missing the required namespace protocol will fail automated verification and require remediation before merge
- Exception process: Exceptions must be documented and reviewed by the technical steering committee for standalone or non-distributed modules
</enforcement>
