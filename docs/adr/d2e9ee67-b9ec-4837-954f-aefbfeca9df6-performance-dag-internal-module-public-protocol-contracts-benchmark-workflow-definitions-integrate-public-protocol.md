---
version: 2
name: d2e9ee67-b9ec-4837-954f-aefbfeca9df6-performance-dag-internal-module-public-protocol-contracts-benchmark-workflow-definitions-integrate-public-protocol
description: "performance_dag Internal Module Public Protocol Contracts: Benchmark Workflow Definitions Integrate Public Protocol"
metadata:
  actual:
    adr_id: d2e9ee67-b9ec-4837-954f-aefbfeca9df6
    status: accepted
    candidate_group_id: 973b25d4-b808-44ac-97ff-9c14df951e29
    evidence:
      source: deep
  scope:
    root: performance
    paths:
      - src/performance_dags/performance_dag/**
---

# performance_dag Internal Module Public Protocol Contracts: Benchmark Workflow Definitions Integrate Public Protocol

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `performance`

Applies to:
- `src/performance_dags/performance_dag/**`

## Context

- Synthetic benchmark workflows require standardized public interfaces to parse runtime environment parameters and validate configuration inputs deterministically.
- The performance_dag module provides public contracts for configuration schema validation and structural graph topology generation across performance testing suites.
- Decoupling configuration validation from graph chaining ensures that invalid environment inputs fail before workflow instantiation begins.

## Problem Statement

Unstructured configuration ingestion and ad-hoc graph creation for benchmark workflows lead to silent misconfigurations, non-reproducible graph structures, and unhandled runtime exceptions during workflow initialization.

## Decision

- **R-PEA-001** MUST: Benchmark workflow definitions MUST integrate public protocol contracts from the performance_dag module for configuration validation and topology generation.

## Policy Block

In scope:

- Workflow definitions and testing modules generating synthetic performance benchmark DAGs.
- Public interfaces consuming performance DAG configuration dictionaries and runtime environment settings.

Out of scope:

- Production workflows with static, non-parameterized task schedules.
- External integration tests not utilizing synthetic benchmark topologies.

## Rationale

- Enforcing validate_performance_dag_conf upfront prevents runtime failures during workflow graph evaluation.
- Standardizing graph topologies via chain_as_binary_tree and chain_as_grid guarantees reproducible benchmark shapes across varying task counts.
- Centralizing duration parsing and convertibility checks eliminates duplicated validation logic across performance suites.

## Consequences

Positive:

- Deterministic and repeatable workflow graph structures across performance evaluations.
- Immediate fail-fast error reporting when mandatory configuration parameters are absent or invalid.
- Consistent task identifier generation preventing workflow compilation collisions.

Negative:

- Topology generation options are constrained to supported chaining protocols.
- Initial initialization overhead from schema validation and environment parameter parsing.

## Alternatives

- Ad-hoc inline environment variable parsing in each benchmark workflow (rejected)
  Rejected because: Leads to code duplication, inconsistent validation rules, and unhandled conversion errors across benchmarks
  When valid: Single-file prototype scripts with no reproducibility requirements
- Static configuration files without dynamic runtime environment injection (rejected)
  Rejected because: Prevents dynamic scaling of task counts, run durations, and graph topologies across automated benchmark matrices
  When valid: Fixed test suites that run identical workflow graphs across all environments

## Risks

- Topology generator functions may encounter scaling bottlenecks with extreme task counts
  Mitigation: Validate maximum task count limits during configuration validation contracts
  Owner: Workflow Engineering Team
- Unsupported operator types specified at runtime cause late DAG generation failures
  Mitigation: Validate operator type strings strictly against supported enumerations during configuration validation
  Owner: Workflow Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Consumers should invoke validate_performance_dag_conf at the entry point of the performance DAG generation pipeline before constructing any tasks.
- Ensure all duration strings comply with standard days, hours, minutes, and seconds regex protocols before passing to configuration dictionaries.

## Continuation Context

Verify commands:

- Discover and run the project test runner to execute unit tests covering performance DAG configuration validation and graph generation protocols.
- Discover and execute static analysis tools across the repository to verify conformance to public interface contracts.

Accept when:

- All unit tests verifying configuration validation contracts pass successfully without errors.
- Topology generation protocols construct valid acyclic task graphs for binary tree and grid configurations.

## Enforcement

- Verified by: Automated continuous integration test suites executing configuration validation tests.
- Verified by: Peer code review checking that benchmark graphs utilize standard public chaining interfaces.
- Violation handling: Pull requests bypassing configuration contracts or chaining protocols are blocked until updated.
- Violation handling: Violations discovered in active branches trigger test failures during workflow compilation.
- Exception process: Submit an architectural review request outlining custom topology requirements not supported by current chaining protocols.
