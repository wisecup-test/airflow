---
version: 2
name: cross-cutting-memory-string-representations-structured-1787
description: "Adoption of Core json Module for Development and Testing Utility Serialization: Memory String Representations Structured Payloads Event"
metadata:
  actual:
    adr_id: 1787b8c3-6ba2-4150-8856-a14e54a0b5b4
    status: accepted
    candidate_group_id: 9ca523a2-d426-417d-a982-d3f05a89ad79
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/**
---

# Adoption of Core json Module for Development and Testing Utility Serialization: Memory String Representations Structured Payloads Event

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/**`.

These rules are ALWAYS ACTIVE for:
- Shared testing utilities and common test harness packages.
- Documentation build extensions and schema verification utilities.
- Mock HTTP clients and external connection helper modules.

### Rules

- **R-LM-001** MUST: In-memory string representations of structured payloads and event metadata MUST be parsed using json.loads.

### Verify

- Discover the project verification script defined in the repository configuration and execute the static analysis suite across shared development and testing modules.
- Discover the test runner invocation from the project manifest and execute the test suite covering utility serialization functions.

**Accept when:**

- All shared test helpers and documentation utilities serialize and deserialize structured data using the core json module without raising format exceptions.
- Static analysis verification demonstrates that no unapproved third-party serialization libraries are imported within shared development utility packages.
- Automated test suites exercising connection serialization, schema validation, and diagnostic log inspection pass cleanly in all execution environments.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks flagging unauthorized external serialization imports in shared testing packages.
- Verified by: Code review inspection of utility pull requests handling connection credentials, mock payloads, and diagnostic logs.
- Verified by: Continuous test suite execution verifying serialization compatibility across supported operating environments.
- Violation handling: Flag pull requests introducing unapproved serialization libraries or ad-hoc payload string concatenation as non-compliant.
- Violation handling: Block merge until external serialization imports are replaced with approved core library interfaces.
- Exception process: Submit an architectural proposal detailing why core serialization capabilities cannot satisfy the functional requirements of the utility.
- Exception process: Demonstrate that the proposed serialization alternative introduces no conflicting runtime requirements or build barriers for existing environments.
- Exception process: Obtain approval from the technical steering group and document the exception within project configuration manifests.
</enforcement>
