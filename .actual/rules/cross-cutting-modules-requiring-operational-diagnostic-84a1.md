---
version: 2
name: cross-cutting-modules-requiring-operational-diagnostic-84a1
description: "Structured Logging via structlog: Modules Requiring Operational Diagnostic Logging Use"
metadata:
  actual:
    adr_id: 84a11c4b-2dbf-4dff-8ca1-ad9180c9470f
    status: accepted
    candidate_group_id: 17f4104d-5c62-49e6-87ad-e62173be120f
    evidence:
      source: deep
  scope:
    root: kubernetes-tests
    paths:
      - lang_sdk/**
      - "!lang_sdk/go_example/**"
      - "!lang_sdk/java_example/**"
---

# Structured Logging via structlog: Modules Requiring Operational Diagnostic Logging Use

Scope: applies to `kubernetes-tests/lang_sdk/**`; excludes `!kubernetes-tests/lang_sdk/go_example/**`, `!kubernetes-tests/lang_sdk/java_example/**`.

These rules are ALWAYS ACTIVE for:
- Operational modules, SDK utilities, and staging routines requiring runtime diagnostic event emission

### Rules

- **R-L-001** MUST: Modules requiring operational or diagnostic logging MUST use structlog to instantiate structured loggers.

### Verify

- Discover the repository static analysis and linting verification scripts and execute them to verify structlog logger initialization compliance.
- Discover and execute the project automated test suites to confirm that structured logging calls execute without schema or runtime errors.

**Accept when:**

- All logger instances in target modules are initialized through structlog.get_logger with logger_name configured to the module namespace
- Project verification and static analysis routines pass without logging-related violations

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in continuous integration pipelines
- Verified by: Peer code review validation during change integration
- Violation handling: Continuous integration builds fail when unapproved logging APIs or direct stream writes are detected
- Violation handling: Code review approval is withheld until logging calls conform to structured logging rules
- Exception process: Submit an architectural review request documenting the technical constraints preventing structlog adoption
- Exception process: Obtain formal authorization from the architecture review board prior to merging fallback logging implementations
</enforcement>
