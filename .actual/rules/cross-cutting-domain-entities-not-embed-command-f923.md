---
version: 2
name: cross-cutting-domain-entities-not-embed-command-f923
description: "dataclasses Configuration Domain Modeling: Domain Entities Not Embed Command Line"
metadata:
  actual:
    adr_id: f9236d67-2ba6-458f-9ee1-2c8cc40f08aa
    status: accepted
    candidate_group_id: 9760a0b9-2198-4e1b-947b-06631217706c
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/ctl/commands/**
---

# dataclasses Configuration Domain Modeling: Domain Entities Not Embed Command Line

Scope: applies to `airflow-ctl/src/airflowctl/ctl/commands/**`.

These rules are ALWAYS ACTIVE for:
- Domain entity representations within configuration inspection and command handlers
- Structured value objects passed between client interfaces and validation routines

### Rules

- **R-DM-001** MUST_NOT: Domain entities MUST NOT embed command line presentation logic or terminal rendering formatting within their class definitions.

### Verify

- Discover and execute the project static analysis test suite to verify domain model type conformity.
- Discover and run the project automated test suite to validate configuration parameter parsing and contract integrity.

**Accept when:**

- All domain entity definitions adhere to typed container declarations without type errors.
- Automated unit tests covering configuration validation and parameter models pass without failures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Static type analysis and linters executed during continuous integration
- Verified by: Peer code review for pull requests modifying domain entities
- Violation handling: Pull requests introducing untyped or inconsistent entity definitions will be blocked in continuous integration until updated to use typed structures.
- Exception process: Submit an architectural review request detailing why structured entity containers cannot satisfy specific runtime constraints.
</enforcement>
