---
version: 2
name: cross-cutting-before-implementing-updating-components-23a8
description: "openapi/requests/types.gen Internal Contract Module Adoption: Before Implementing Updating Components Relying Contract"
metadata:
  actual:
    adr_id: 23a8f780-addc-435e-a40a-7e6e2d06ee2b
    status: accepted
    candidate_group_id: a801354c-7903-4feb-92e8-39ee937f33e4
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# openapi/requests/types.gen Internal Contract Module Adoption: Before Implementing Updating Components Relying Contract

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Client presentation components, modal dialogs, status badges, and graphing utilities that consume or send backend API data
- Domain type definitions and client-side data transformation functions operating on service entities

### Rules

- **R-LM-001** MUST: Before implementing or updating components relying on contract definitions, the consumer MUST inspect the repository dependency lock artifact to resolve the authoritative locked versions of all code generation and contract dependencies.

### Verify

- echo 'Discover the project manifest and execute the designated type-checking script to verify contract conformance across all client modules.'
- echo 'Discover and run the project build validation command to verify that all imports from the generated contract module resolve correctly.'

**Accept when:**

- All client components importing from openapi/requests/types.gen compile without static type errors.
- The project build and type-checking scripts exit with return code zero during automated verification.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration type-checking and automated build verification passes.
- Verified by: Peer review checks ensuring new or modified UI components import contract types exclusively from openapi/requests/types.gen.
- Violation handling: Automated pull request checks fail when type inconsistencies or broken contract references are detected.
- Violation handling: Code reviews block merge of pull requests that introduce duplicate manual interface declarations for API entities.
- Exception process: Exceptions require architectural review and approval documented in an issue tracker with a planned migration timeline.
</enforcement>
