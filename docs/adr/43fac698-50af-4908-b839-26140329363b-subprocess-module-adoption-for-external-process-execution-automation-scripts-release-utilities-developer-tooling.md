---
version: 2
name: 43fac698-50af-4908-b839-26140329363b-subprocess-module-adoption-for-external-process-execution-automation-scripts-release-utilities-developer-tooling
description: "Subprocess Module Adoption for External Process Execution: Automation Scripts Release Utilities Developer Tooling"
metadata:
  actual:
    adr_id: 43fac698-50af-4908-b839-26140329363b
    status: accepted
    candidate_group_id: a726c4f0-4561-4f9b-ab7e-fe47bbbe758f
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - assign_cherry_picked_prs_with_milestone.py
      - breeze/src/airflow_breeze/**
      - prune_old_dirs.py
      - registry/**
      - retag_docker_images.py
---

# Subprocess Module Adoption for External Process Execution: Automation Scripts Release Utilities Developer Tooling

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `assign_cherry_picked_prs_with_milestone.py`
- `breeze/src/airflow_breeze/**`
- `prune_old_dirs.py`
- `registry/**`
- `retag_docker_images.py`

## Context

- Development automation, release verification, and distribution packaging require consistent invocation of host system binaries and external utilities.
- Direct shell execution or unstandardized command invocation creates vulnerabilities around argument escaping, unhandled process failures, and inconsistent stream capture.
- Codebase evidence across multiple automation modules shows widespread reliance on the subprocess module to orchestrate external tasks and inspect process outputs.

## Problem Statement

Development workflows, build steps, and release integrity verification require executing external system processes across heterogeneous environments. Without a standardized process execution interface and disciplined stream handling, commands risk silent failures, injection vulnerabilities through shell string interpolation, and uncaptured error diagnostics.

## Decision

- **R-LM-001** MUST: All automation scripts, release utilities, and developer tooling modules MUST use the subprocess module as the standard interface for external system process execution.

## Policy Block

In scope:

- All automation scripts, release management modules, and developer tooling invoking external system executables.

Out of scope:

- Pure internal library computations and in-memory operations that do not invoke external executables.

Exceptions:

- **EX-20-001** External interactive prompts require raw terminal inheritance rather than piped stream capture.
  Approval: Tooling maintainer approval
  Documentation: Documented inline within the calling utility module

## Rationale

- Nineteen modules across development tooling, packaging, and release verification demonstrate consistent adoption of the subprocess module for external command orchestration.
- Using the core subprocess module avoids external dependencies while offering secure argument vector separation and robust stream capture.
- Structured process execution prevents unintended shell evaluation and provides deterministic return code inspection for build and release automation.

## Consequences

Positive:

- Standardizes external command invocation across all development, release, and packaging modules.
- Mitigates command injection vulnerabilities by mandating structured argument sequences instead of shell string evaluation.
- Ensures consistent inspection of exit codes and structured capture of standard output and error streams.

Negative:

- Requires boilerplate argument array construction and stream capture configuration across utility scripts.
- Requires explicit prerequisite checking to ensure required external executables exist on the host platform.

## Alternatives

- Direct shell execution through legacy OS system commands (rejected)
  Rejected because: Exposes execution to shell injection risks, lacks fine-grained stream redirection, and provides inadequate return code handling.
  When valid: Trivial one-off debugging scripts where process output and safety constraints are unimportant.
- Third-party external process wrapper libraries (rejected)
  Rejected because: Introduces unnecessary external dependencies for capabilities natively supported by the standard library core module.
  When valid: Environments requiring asynchronous process streaming or complex interactive pseudo-terminal orchestration.

## Risks

- External process execution may hang if subprocess output buffers exceed system pipe limits without proper stream drainage.
  Mitigation: Utilize built-in standard stream capture parameters or helper wrappers that consume process output before completion.
  Owner: Engineering Team
- Variability in host system binary availability can cause unexpected process execution failures.
  Mitigation: Perform pre-execution binary existence and prerequisite validation prior to invoking external processes.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Wrap recurring subprocess invocation patterns in shared project utility functions with consistent error reporting and stream formatting.
- Avoid shell execution flags unless shell builtins or pipeline features are strictly necessary and arguments are safely sanitized.

## Continuation Context

Verify commands:

- # Discover the project test runner and execute the test suite covering process execution and release validation modules
- # Discover and run the project static analysis linter to verify process execution safety rules
- # Discover and execute integration tests to ensure external process wrappers handle non-zero exit codes correctly

Accept when:

- All unit and integration test suites covering process execution modules pass without unhandled process exceptions.
- Static analysis verification confirms zero unescaped shell executions or uninspected return codes across automation modules.
- Process execution helpers correctly capture output streams and report non-zero return codes.

## Enforcement

- Verified by: Automated continuous integration test suites
- Verified by: Static analysis and linter checks
- Verified by: Peer code review for automation and tooling pull requests
- Violation handling: Continuous integration failure upon detection of non-compliant process execution or lint violations
- Violation handling: Mandatory pull request remediation prior to merge
- Exception process: Submit an architectural review request documenting the specific requirement for non-standard process execution.
- Exception process: Approval required from development tooling maintainers.
