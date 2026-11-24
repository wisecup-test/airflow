---
version: 2
name: cross-cutting-configuration-scripts-not-leak-sensitive-f85c
description: "docs.provider_conf Module Adoption for Provider Documentation Configuration: Configuration Scripts Not Leak Sensitive Runtime"
metadata:
  actual:
    adr_id: f85c1b19-814b-43bf-a2b9-4bdf991f0ef3
    status: accepted
    candidate_group_id: 69820d2d-be82-4c26-a849-9c2de1bd8f6d
    evidence:
      source: deep
  scope:
    root: providers/apache/flink
    paths:
      - docs/**
---

# docs.provider_conf Module Adoption for Provider Documentation Configuration: Configuration Scripts Not Leak Sensitive Runtime

Scope: applies to `providers/apache/flink/docs/**`.

These rules are ALWAYS ACTIVE for:
- Documentation configuration modules within provider packages requiring shared configuration settings.

### Rules

- **R-LM-001** MUST_NOT: Configuration scripts MUST_NOT leak sensitive runtime configuration values or secrets retrieved from os.environ into generated build artifacts.

### Verify

- Discover and run repository static analysis scripts to verify configuration import conventions across documentation modules.
- Discover and run repository documentation build scripts to confirm successful compilation with required environment variables.

**Accept when:**

- Static analysis checks pass with zero syntax or import violations across documentation configuration modules.
- Documentation build processes execute to completion without encountering unhandled environment variable lookup exceptions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test suites and static analysis passes.
- Verified by: Peer code review on all pull requests modifying documentation configuration modules.
- Violation handling: Automated build failures in validation pipelines blocking pull request merges.
- Violation handling: Required code remediation during review cycles to eliminate direct unvalidated lookups.
- Exception process: Submit an architectural exception request detailing why custom documentation configuration is necessary.
- Exception process: Obtain documented review and approval from repository maintainers before merging overrides.
</enforcement>
