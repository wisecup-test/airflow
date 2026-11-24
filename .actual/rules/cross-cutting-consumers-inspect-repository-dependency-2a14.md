---
version: 2
name: cross-cutting-consumers-inspect-repository-dependency-2a14
description: "Vitest Unit Testing Framework Adoption: Consumers Inspect Repository Dependency Lock Artifact"
metadata:
  actual:
    adr_id: 2a146927-eb02-4fa5-885d-280d3a32a110
    status: accepted
    candidate_group_id: 633bdaec-608d-412c-909e-0c65956530b5
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Vitest Unit Testing Framework Adoption: Consumers Inspect Repository Dependency Lock Artifact

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Unit and component test suites within frontend user interface packages and authentication manager UI modules.
- Test runner configurations and test lifecycle setup modules for client-side codebases.

### Rules

- **R-TS-001** MUST: Consumers MUST inspect the repository dependency lock artifact to resolve the exact locked version of Vitest before configuring or executing test suites.

### Verify

- git grep -E '(from "vitest"|from "vitest/config")'
- grep -rn "defineConfig" --include="*.ts" .

**Accept when:**

- Frontend test configuration modules successfully configure test environments using defineConfig from vitest/config.
- All unit test suites utilizing Vitest assertion and lifecycle hooks execute cleanly without unhandled rejections.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test execution stages validating all frontend test suites.
- Verified by: Automated repository linting rules prohibiting non-standard test runners and assertion libraries in frontend packages.
- Violation handling: Pull requests introducing alternative test runners or unaligned configuration patterns fail continuous integration verification and are blocked from merge.
- Exception process: Exceptions require written architectural justification and formal sign-off from frontend engineering leads before code merge.
</enforcement>
