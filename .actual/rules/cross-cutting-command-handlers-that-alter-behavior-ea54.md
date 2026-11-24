---
version: 2
name: cross-cutting-command-handlers-that-alter-behavior-ea54
description: "click Custom Parameter Type Specialization: Command Handlers That Alter Behavior Based"
metadata:
  actual:
    adr_id: ea545402-fadd-4828-a857-c244e3f34c19
    status: accepted
    candidate_group_id: a08140a8-4566-4c2a-abb6-42f574c85d9c
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/commands/**
---

# click Custom Parameter Type Specialization: Command Handlers That Alter Behavior Based

Scope: applies to `dev/breeze/src/airflow_breeze/commands/**`.

These rules are ALWAYS ACTIVE for:
- Option definitions and parameter declarations across command-line interface command modules.
- Interactive prompts and choices within command-line tools.

### Rules

- **R-LM-001** SHOULD: Command handlers that alter behavior based on parameter origin SHOULD verify option provenance using ctx.get_parameter_source.

### Verify

- Discover and execute the project test runner to validate command-line option parsing and custom parameter type behavior.
- Discover and run the project code linters to detect unvalidated string options across command modules.

**Accept when:**

- All command-line options requiring restricted inputs utilize specialized choice parameter types and reject invalid values with standardized error messages.
- All test suites verifying command option parsing and parameter source inspection pass successfully.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites executing command-line parsing verification.
- Verified by: Static analysis checks enforcing parameter typing conventions.
- Verified by: Peer code review on all pull requests modifying command option definitions.
- Violation handling: Pull requests introducing unconstrained primitive parameters for bounded options are blocked until converted to custom parameter types.
- Violation handling: Continuous integration failures when parameter validation tests do not pass.
- Exception process: Exceptions require approval from the tooling maintainers with written rationale explaining why domain validation cannot be handled by custom parameter types.
</enforcement>
