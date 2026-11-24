---
version: 2
name: cross-cutting-when-referencing-any-versioned-dependenc-96db
description: "Environment Variable Access via os.environ: When Referencing Any Versioned Dependency Consumer"
metadata:
  actual:
    adr_id: 96db927b-5455-4302-8937-38224e6acc30
    status: accepted
    candidate_group_id: 3c498080-850e-4117-bdf0-a12dc4d37e55
    evidence:
      source: deep
  scope:
    root: providers/apache/druid
    paths:
      - docs/**
---

# Environment Variable Access via os.environ: When Referencing Any Versioned Dependency Consumer

Scope: applies to `providers/apache/druid/docs/**`.

These rules are ALWAYS ACTIVE for:
- Documentation build configuration scripts requiring package identity parameters from the execution environment.

### Rules

- **R-SC-001** MUST: When referencing any versioned dependency, the consumer MUST discover the ecosystem lock file and verify the resolved version before implementation.

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
