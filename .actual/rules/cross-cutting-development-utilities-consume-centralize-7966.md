---
version: 2
name: cross-cutting-development-utilities-consume-centralize-7966
description: "airflow_breeze Internal Development and Automation Module Adoption: Development Utilities Consume Centralized Configuration Option"
metadata:
  actual:
    adr_id: 79664422-f386-45a7-a95a-405d2fc43edf
    status: accepted
    candidate_group_id: bd5c6587-0bf4-453a-b93b-712c0aaaef76
    evidence:
      source: deep
  scope:
    root: dev
---

# airflow_breeze Internal Development and Automation Module Adoption: Development Utilities Consume Centralized Configuration Option

Scope: applies to `dev/**`.

These rules are ALWAYS ACTIVE for:
- Development environment automation scripts and commands within the repository
- Shared release management, documentation publishing, and testing utilities

### Rules

- **R-LM-001** MUST: Development utilities MUST consume centralized configuration and option handlers from airflow_breeze shared utilities rather than inspecting runtime environments directly in ad-hoc scripts.

### Verify

- Discover the repository test runner from the project manifest and execute the test suite targeting the internal development automation module.
- Locate and run the project code quality and style linters to verify compliance with module import and boundary standards.

**Accept when:**

- All unit and integration tests for the internal module pass successfully under the discovered test runner.
- Static analysis checks verify that shared automation logic imports from centralized utility modules without boundary violations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites and static analysis linters validating module contracts
- Verified by: Peer review on pull requests touching development automation packages
- Violation handling: Pull requests introducing unencapsulated standalone scripts or bypassing centralized utilities must be blocked until refactored into the module structure.
- Exception process: Exceptions for temporary bootstrap scripts require documented justification and approval from repository maintainers.
</enforcement>
