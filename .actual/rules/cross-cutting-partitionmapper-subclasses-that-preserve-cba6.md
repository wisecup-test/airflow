---
version: 2
name: cross-cutting-partitionmapper-subclasses-that-preserve-cba6
description: "Adoption of airflow.partition_mappers Framework for Asset Partition Key Translation and Composition: Partitionmapper Subclasses That Preserve Establish Temporal"
metadata:
  actual:
    adr_id: cba6f224-3cfe-4c41-a2a5-148f7bb3f879
    status: accepted
    candidate_group_id: 5fbc0798-2328-4d47-a466-b9fe47a7755f
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow.partition_mappers Framework for Asset Partition Key Translation and Composition: Partitionmapper Subclasses That Preserve Establish Temporal

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All partition key translation logic between upstream and downstream partitioned assets.
- Composition of sequential and multi-dimensional partition key mappings.
- Plugin-based custom partition mapper extensions.

### Rules

- **R-LM-001** MUST: PartitionMapper subclasses that preserve or establish temporal anchors MUST implement carry_partition_date or to_partition_date to resolve partition timestamps without introducing temporal ambiguity.

### Verify

- echo "Discover and execute the repository test runner targeting partition mapper classes"
- echo "Discover and execute the repository type validation suite against PartitionMapper contracts"
- echo "Discover and execute serialization roundtrip tests for custom and composite partition mappers"

**Accept when:**

- All unit and integration tests for PartitionMapper implementations pass without failures.
- Every PartitionMapper subclass successfully executes serialize and deserialize roundtrips preserving all internal parameters.
- ChainMapper and ProductMapper composite hierarchies resolve downstream keys and temporal partition dates consistently across configured dimensions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated unit and integration test suites validating partition mapper contracts and serialization fidelity.
- Verified by: Pull request code reviews ensuring new partition mappers adhere to PartitionMapper inheritance and serialization protocols.
- Violation handling: Pull requests lacking required serialization methods or contract conformance will be rejected during automated checks.
- Violation handling: Non-compliant partition translation implementations discovered in production will be flagged for refactoring.
- Exception process: Exceptions must be submitted via an architecture review request detailing the technical blocker and migration roadmap.
</enforcement>
