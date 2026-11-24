---
version: 2
name: cross-cutting-domain-contract-models-inherit-pydantic-dd58
description: "Pydantic Strict Contract Validation Models: Domain Contract Models Inherit Pydantic Basemodel"
metadata:
  actual:
    adr_id: dd581841-4696-4569-9dbe-df3e1ce6a5db
    status: accepted
    candidate_group_id: c2be38ea-a209-41e6-baed-8a5520252127
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - registry/**
---

# Pydantic Strict Contract Validation Models: Domain Contract Models Inherit Pydantic Basemodel

Scope: applies to `dev/registry/**`.

These rules are ALWAYS ACTIVE for:
- All domain contract models representing external registry entities, catalog definitions, and distribution metadata.
- Validation routines that parse, verify, and enforce integrity rules on domain contract structures.

### Rules

- **R-BRE-001** MUST: Domain contract models MUST inherit from Pydantic BaseModel and configure model_config with extra set to forbid to reject any undeclared attributes during instantiation.

### Verify

- Discover the project test runner configuration and execute test suites targeting contract model validation suites.
- Discover the static type analysis configuration and run the project type checking suite across all domain model packages.
- Discover and run the project schema integrity verification scripts against all sample catalog fixtures.

**Accept when:**

- All contract models reject instantiation when undeclared attributes are supplied.
- Static type analysis passes across all domain contract definitions with zero type errors.
- All catalog validation test suites execute and pass successfully.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline executing unit tests, contract integrity checks, and static type analysis.
- Verified by: Mandatory peer code review focusing on contract schema definitions and ConfigDict configurations.
- Violation handling: Pull requests introducing contract models without extra set to forbid or lacking strict type annotations are blocked from merging.
- Violation handling: Runtime contract validation failures trigger immediate rejection of invalid data payloads with descriptive error traces.
- Exception process: Submit an exception request to the domain architecture review board detailing the backward compatibility need, proposed mitigation, and timeline for schema alignment.
</enforcement>
