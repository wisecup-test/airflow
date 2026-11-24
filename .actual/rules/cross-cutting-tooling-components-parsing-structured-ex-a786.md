---
version: 2
name: cross-cutting-tooling-components-parsing-structured-ex-a786
description: "Standard Library json and tomllib Deserialization for Input Parsing: Tooling Components Parsing Structured External Local"
metadata:
  actual:
    adr_id: a78635b5-c08d-453f-954e-4a73ab242a69
    status: accepted
    candidate_group_id: 6f654889-96c1-4479-a7fe-0bd5404196ca
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - registry/**
      - stats/**
---

# Standard Library json and tomllib Deserialization for Input Parsing: Tooling Components Parsing Structured External Local

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`, `dev/stats/**`.

These rules are ALWAYS ACTIVE for:
- All utility scripts, build tools, and metadata generators that ingest structured file formats or remote API payloads.

### Rules

- **R-SCP-001** MUST: Tooling components parsing structured external or local data MUST validate input payloads against defined schemas or typed contract models before consuming nested fields.

### Verify

- Discover the repository static analysis suite and execute verification against structured input parsing routines.
- Discover and run the project test runner to validate that data deserialization contracts enforce input integrity.

**Accept when:**

- Verification suite succeeds with zero schema validation failures across structured data loaders.
- All external data deserialization entry points enforce strict typing and reject malformed payloads.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration static analysis checks scanning for unsafe deserialization functions.
- Verified by: Automated test suites executing schema validation against sample configuration files and API responses.
- Verified by: Peer code review verifying that new data ingestion endpoints incorporate appropriate validation boundaries.
- Violation handling: Automated static checks fail builds that invoke disallowed deserialization functions.
- Violation handling: Pull requests lacking contract validation for public-facing data ingestion are blocked from merging until compliant schemas are supplied.
- Exception process: Submit an architectural exception request detailing why strict schema validation is infeasible for the specific script, subject to review and sign-off by the security team.
</enforcement>
