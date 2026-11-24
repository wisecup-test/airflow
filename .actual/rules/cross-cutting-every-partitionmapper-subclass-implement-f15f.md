---
version: 2
name: cross-cutting-every-partitionmapper-subclass-implement-f15f
description: "Adoption of airflow.partition_mappers Framework for Asset Partition Key Translation and Composition: Every Partitionmapper Subclass Implement Downstream Method"
metadata:
  actual:
    adr_id: f15fa8d1-7728-4177-b3bd-881d23534f67
    status: accepted
    candidate_group_id: 5fbc0798-2328-4d47-a466-b9fe47a7755f
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow.partition_mappers Framework for Asset Partition Key Translation and Composition: Every Partitionmapper Subclass Implement Downstream Method

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All partition key translation logic between upstream and downstream partitioned assets.
- Composition of sequential and multi-dimensional partition key mappings.
- Plugin-based custom partition mapper extensions.

### Rules

- **R-LM-001** MUST: Every PartitionMapper subclass MUST implement the to_downstream method to map an upstream partition key string to a downstream partition key string or an iterable of downstream partition key strings.

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
