---
version: 2
name: cross-cutting-custom-parameter-protocol-subclasses-ove-48ef
description: "Click Custom Choice Protocol for Command-Line Interfaces: Custom Parameter Protocol Subclasses Override Get"
metadata:
  actual:
    adr_id: 48ef11f8-2586-4039-bfac-8197dec9280e
    status: accepted
    candidate_group_id: 2a7bee18-98df-43fe-8591-18ced454e09b
    evidence:
      source: deep
  scope:
    root: dev
---

# Click Custom Choice Protocol for Command-Line Interfaces: Custom Parameter Protocol Subclasses Override Get

Scope: applies to `dev/**`.

These rules are ALWAYS ACTIVE for:
- Public and external command-line interface parameter definitions requiring choice enumeration, custom metavar formatting, or dynamic shell completion.

### Rules

- **R-PEA-001** SHOULD: Custom parameter protocol subclasses SHOULD override get_metavar to enforce clear visual separation and delimiter formatting in generated command-line help documentation.

### Verify

- Discover the project test runner and execute the test suite validating parameter type conversion and metavar generation.
- Discover the static analysis tool and verify that command definitions utilize registered parameter type subclasses without inline argument parsing.

**Accept when:**

- All parameter type verification tests pass, confirming expected value conversion, token normalization, and metavar formatting.
- Command-line help generation produces well-formatted parameter signatures without display wrapping degradation.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites validating option conversion, token normalization, and metavar generation.
- Verified by: Code review inspection of command parameter declarations.
- Violation handling: Pull requests introducing manual argument validation in command bodies or improper choice types are rejected during review.
- Exception process: Submit an architectural review request justifying why standard or custom parameter type protocols cannot support the input requirements.
</enforcement>
