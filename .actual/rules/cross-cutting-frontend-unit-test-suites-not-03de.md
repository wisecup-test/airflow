---
version: 2
name: cross-cutting-frontend-unit-test-suites-not-03de
description: "Vitest Unit Testing Framework Adoption: Frontend Unit Test Suites Not Depend"
metadata:
  actual:
    adr_id: 03de360e-caca-4cf1-bcba-816671e94fcf
    status: accepted
    candidate_group_id: 633bdaec-608d-412c-909e-0c65956530b5
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Vitest Unit Testing Framework Adoption: Frontend Unit Test Suites Not Depend

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Unit and component test suites within frontend user interface packages and authentication manager UI modules.
- Test runner configurations and test lifecycle setup modules for client-side codebases.

### Rules

- **R-TS-001** MUST_NOT: Frontend unit test suites MUST NOT depend on live external network services or running backend services during unit test execution.

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
