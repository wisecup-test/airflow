---
version: 2
name: cross-cutting-consumers-discover-repository-lock-artif-c692
description: "enum Module Adoption for Strongly-Typed Enumerations: Consumers Discover Repository Lock Artifact Verify"
metadata:
  actual:
    adr_id: c692471a-6030-4974-a42a-79f214c0dd55
    status: accepted
    candidate_group_id: 00934b03-7054-425c-a97f-e7ecbe4ac60e
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/api/**
---

# enum Module Adoption for Strongly-Typed Enumerations: Consumers Discover Repository Lock Artifact Verify

Scope: applies to `airflow-ctl/src/airflowctl/api/**`.

These rules are ALWAYS ACTIVE for:
- Client boundary definitions and public request dispatchers requiring fixed execution modes
- Data transfer models and contract specifications defining bounded sets of categorical values

### Rules

- **R-LM-001** MUST: Consumers MUST discover the repository lock artifact and verify the exact runtime language and environment resolution before implementing or extending enumeration definitions.

### Verify

- Discover and execute the repository test suite to verify that all enumeration classes instantiate with expected member values.
- Discover and run the project static type checker to confirm exhaustive handling and correct type annotations across all enumeration usages.
- Discover and run the project linter and contract verification routines to validate that serialized enumeration members match schema expectations.

**Accept when:**

- All enumeration member definitions resolve to valid values and pass automated unit tests without regression.
- The project static type verification completes with zero type errors related to enumeration assignments.
- Network serialization and deserialization cycles preserve enumeration value fidelity across client boundaries.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static type analysis executed during repository integration pipelines.
- Verified by: Peer code review verifying that new discrete states subclass enum.Enum rather than using loose string constants.
- Violation handling: Pull requests introducing loose primitive strings for bounded modes will be blocked until refactored into enumeration members.
- Violation handling: Static type analysis failures triggered by invalid enumeration values halt pipeline progression.
- Exception process: Submit an architectural review request detailing why dynamic strings or loose constants are required over structured enumerations.
- Exception process: Exception requests must be approved by the client architecture maintainers and documented in the repository review record.
</enforcement>
