---
version: 2
name: cross-cutting-internal-tooling-documentation-modules-d-11b6
description: "devel-common Runtime Environment Variable Configuration Management: Internal Tooling Documentation Modules Devel Common"
metadata:
  actual:
    adr_id: 11b69c5e-20ba-420c-aaa0-7c7fe424d433
    status: accepted
    candidate_group_id: a119244d-841c-40b7-af5a-4e263ca819d7
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/**
      - src/sphinx_exts/**
---

# devel-common Runtime Environment Variable Configuration Management: Internal Tooling Documentation Modules Devel Common

Scope: applies to `devel-common/src/docs/**`, `devel-common/src/sphinx_exts/**`.

These rules are ALWAYS ACTIVE for:
- Internal developer tooling modules within devel-common
- Documentation generation and extension configuration scripts
- Local and pipeline execution environments where dynamic runtime context is supplied via environment variables

### Rules

- **R-CEM-001** MUST: Internal tooling and documentation modules in devel-common MUST source dynamic execution configuration and external path overrides through the standard os.environ mapping or os.getenv accessor.

### Verify

- Discover the project test execution runner from repository configuration and execute the unit test suite covering configuration and environment handling.
- Discover and run the repository static analysis and linting scripts to verify compliance with environment access conventions.
- Execute the documentation generation workflow in an environment with clean configuration variables to verify fallback resolution.

**Accept when:**

- The project test suite passes with all environment resolution tests succeeding.
- Static analysis checks pass with no unhandled environment access violations.
- Documentation generation succeeds with default environment fallback paths.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline execution covering test suites
- Verified by: Static analysis rule checks on pull requests
- Verified by: Peer code review verification of environment access patterns
- Violation handling: Pull requests with unvalidated environment variable access or missing fallbacks are blocked from merging until corrected
- Violation handling: Build failures triggered by missing environment variables must be resolved by adding explicit exceptions or defaults
- Exception process: Submit an architectural review request to the tooling maintainers outlining the rationale for structured configuration alternatives
- Exception process: Record accepted exceptions in the module policy registry
</enforcement>
