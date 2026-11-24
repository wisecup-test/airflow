---
version: 2
name: cross-cutting-developers-not-define-local-duplicate-4c91
description: "openapi/requests/types.gen Internal Contract Module Adoption: Developers Not Define Local Duplicate Typescript"
metadata:
  actual:
    adr_id: 4c91b062-f4a9-4c92-b703-692fb48de88b
    status: accepted
    candidate_group_id: a801354c-7903-4feb-92e8-39ee937f33e4
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# openapi/requests/types.gen Internal Contract Module Adoption: Developers Not Define Local Duplicate Typescript

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Client presentation components, modal dialogs, status badges, and graphing utilities that consume or send backend API data
- Domain type definitions and client-side data transformation functions operating on service entities

### Rules

- **R-LM-001** MUST_NOT: Developers MUST NOT define local duplicate TypeScript interfaces, types, or ad-hoc data models for entities already defined in the openapi/requests/types.gen module.

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
