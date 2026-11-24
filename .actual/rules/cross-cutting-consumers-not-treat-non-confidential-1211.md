---
version: 2
name: cross-cutting-consumers-not-treat-non-confidential-1211
description: "Environment Variable Access via os.environ: Consumers Not Treat Non Confidential Package"
metadata:
  actual:
    adr_id: 1211b858-cb51-4245-af2c-e3f41f44c161
    status: accepted
    candidate_group_id: 3c498080-850e-4117-bdf0-a12dc4d37e55
    evidence:
      source: deep
  scope:
    root: providers/apache/druid
    paths:
      - docs/**
---

# Environment Variable Access via os.environ: Consumers Not Treat Non Confidential Package

Scope: applies to `providers/apache/druid/docs/**`.

These rules are ALWAYS ACTIVE for:
- Documentation build configuration scripts requiring package identity parameters from the execution environment.

### Rules

- **R-SC-001** MUST_NOT: Consumers MUST NOT treat non-confidential package naming parameters read via os.environ as confidential secrets or credentials.

### Verify

- Discover and run the project static analysis and linting suite to check for unhandled environment variable access patterns.
- Discover and execute the repository documentation build workflow to verify environment variable resolution passes without errors.

**Accept when:**

- Static analysis checks pass with zero unhandled environment variable access errors.
- Documentation build passes successfully when required environment variables are set.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis in CI pipelines
- Verified by: Code review approval prior to merging
- Violation handling: Flagged pull requests must be corrected or reviewed for false-positive secret classification.
- Exception process: Architectural review approval for specialized configuration injection requirements.
</enforcement>
