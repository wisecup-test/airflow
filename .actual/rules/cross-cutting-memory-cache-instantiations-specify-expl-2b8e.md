---
version: 2
name: cross-cutting-memory-cache-instantiations-specify-expl-2b8e
description: "TTLCache and LRUCache In-Memory Caching for Primary Datastore Access: Memory Cache Instantiations Specify Explicit Bounded"
metadata:
  actual:
    adr_id: 2b8ec38f-9103-4a2c-9c7a-0046050d60ad
    status: accepted
    candidate_group_id: 89bddde6-c678-4ce7-9990-40869c530890
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# TTLCache and LRUCache In-Memory Caching for Primary Datastore Access: Memory Cache Instantiations Specify Explicit Bounded

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Primary datastore access layers, ORM entity queries, and model deserialization routines evaluated in high-frequency execution loops.
- Repeated queries for static or slowly mutating configuration, schema definitions, and entity ownership mappings.

### Rules

- **R-PD-001** MUST: All in-memory cache instantiations MUST specify explicit bounded capacity via maxsize and, for volatile or mutating datastore entities, an explicit time-to-live parameter via ttl.

### Verify

- Discover the repository test runner from the project manifests and execute unit test suites covering the datastore caching layers.
- Discover and run static analysis linters across the codebase to ensure all cache definitions define explicit maxsize limits.

**Accept when:**

- Primary datastore query invocations in recurring execution loops resolve from in-memory caches upon repeated lookups.
- All cache declarations configure explicit maximum size constraints and, where required for mutable records, explicit expiration intervals.
- Cache telemetry confirms operational hits and misses without unbounded memory growth or process memory exhaustion.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated linting rules detecting unbounded in-memory collection growth and missing capacity parameters.
- Verified by: Code review verification for all new primary datastore read paths in recurring execution loops.
- Verified by: Integration test suites asserting that repeated entity retrievals do not increment primary datastore query counters.
- Violation handling: Code reviews reject un-cached repetitive datastore queries in high-frequency execution paths.
- Violation handling: Pull requests introducing unbounded caching collections are blocked until bounded capacity and eviction policies are configured.
- Exception process: Submit an architectural exception request documenting why direct primary datastore round-trips or unbounded storage are required.
- Exception process: Obtain sign-off from the technical leads following query impact analysis and benchmark evaluations on datastore capacity.
</enforcement>
