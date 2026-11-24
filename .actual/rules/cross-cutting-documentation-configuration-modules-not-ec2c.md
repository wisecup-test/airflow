---
version: 2
name: cross-cutting-documentation-configuration-modules-not-ec2c
description: "docs.provider_conf Module Adoption for Documentation Configuration: Documentation Configuration Modules Not Access Sensitive"
metadata:
  actual:
    adr_id: ec2cbd6e-37bd-41f5-b351-f6494b50de5b
    status: accepted
    candidate_group_id: 027ba7c8-dfad-44a4-b08b-e3d55cefcd73
    evidence:
      source: deep
  scope:
    root: providers/apache/druid
    paths:
      - docs/**
---

# docs.provider_conf Module Adoption for Documentation Configuration: Documentation Configuration Modules Not Access Sensitive

Scope: applies to `providers/apache/druid/docs/**`.

These rules are ALWAYS ACTIVE for:
- Documentation configuration modules within provider packages.
- Configurations integrating with the docs.provider_conf internal documentation module.

### Rules

- **R-LM-001** MUST_NOT: Documentation configuration modules MUST_NOT access sensitive runtime credentials or store secrets directly in configuration source files.

### Verify

- Discover and run the project documentation build script to verify configuration validity.
- Discover and execute the repository static analysis and configuration linter scripts to ensure compliance with documentation standards.

**Accept when:**

- The documentation build suite executes without configuration or import errors.
- All documentation configurations successfully resolve shared settings from docs.provider_conf.
- Environment variable lookups succeed or fail gracefully with documented fallbacks.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration documentation build checks.
- Verified by: Static analysis checks verifying imports and environment variable access patterns.
- Verified by: Peer code review for changes to documentation configurations.
- Violation handling: Pull requests with non-compliant documentation configurations fail automated verification.
- Violation handling: Violations must be refactored to use the shared docs.provider_conf module before merge.
- Exception process: Submit an architectural exception request detailing why the shared module cannot be utilized.
- Exception process: Obtain sign-off from documentation architecture leads before proceeding.
</enforcement>
