---
version: 2
name: cross-cutting-modules-not-emit-diagnostic-logs-0998
description: "Structured Logging via structlog: Modules Not Emit Diagnostic Logs Through"
metadata:
  actual:
    adr_id: 09989386-4c2f-4eb4-b3f5-bfa4503cb3bd
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

# Structured Logging via structlog: Modules Not Emit Diagnostic Logs Through

Scope: applies to `kubernetes-tests/lang_sdk/**`; excludes `!kubernetes-tests/lang_sdk/go_example/**`, `!kubernetes-tests/lang_sdk/java_example/**`.

These rules are ALWAYS ACTIVE for:
- Operational modules, SDK utilities, and staging routines requiring runtime diagnostic event emission

### Rules

- **R-L-001** MUST_NOT: Modules MUST_NOT emit diagnostic logs through direct standard output stream writes or unconfigured default root loggers.

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
