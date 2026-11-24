---
version: 2
name: cross-cutting-documentation-configuration-access-host-be5b
description: "Environment Variable Access via os.environ: Documentation Configuration Access Host Environment Variables"
metadata:
  actual:
    adr_id: be5b6390-b63a-42ea-a298-9d24a6dd60aa
    status: accepted
    candidate_group_id: 3c498080-850e-4117-bdf0-a12dc4d37e55
    evidence:
      source: deep
  scope:
    root: providers/apache/druid
    paths:
      - docs/**
---

# Environment Variable Access via os.environ: Documentation Configuration Access Host Environment Variables

Scope: applies to `providers/apache/druid/docs/**`.

These rules are ALWAYS ACTIVE for:
- Documentation build configuration scripts requiring package identity parameters from the execution environment.

### Rules

- **R-SC-001** MUST: The documentation configuration MUST access host environment variables through the standard os.environ mapping when resolving package metadata at generation time.

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
