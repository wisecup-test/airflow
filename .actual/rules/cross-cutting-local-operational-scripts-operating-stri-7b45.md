---
version: 2
name: cross-cutting-local-operational-scripts-operating-stri-7b45
description: "Standard Library json and tomllib Deserialization for Input Parsing: Local Operational Scripts Operating Strictly Repository"
metadata:
  actual:
    adr_id: 7b453f42-c417-4cdb-aa03-c92a39286f36
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

# Standard Library json and tomllib Deserialization for Input Parsing: Local Operational Scripts Operating Strictly Repository

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`, `dev/stats/**`.

These rules are ALWAYS ACTIVE for:
- All utility scripts, build tools, and metadata generators that ingest structured file formats or remote API payloads.

### Rules

- **R-SCP-001** MAY: Local operational scripts operating strictly on repository-controlled static configuration MAY utilize standard library parsers without external schema enforcement when data integrity is assured by repository version control.

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
