# cache

TTL-based key-value cache in front of expensive reads and computations.

## Purpose

Reduce latency and repeated work by memoizing hot values with explicit expiry and invalidation.

## Responsibilities

- Provide namespaced get/set with TTL.
- Invalidate by key, prefix, or tag.
- Protect backends by coalescing concurrent misses (single-flight).
- Expose hit/miss metrics per namespace.

## Data model

In-memory / remote KV; not a relational store. Keys follow `<module>:<entity>:<id>[:<variant>]`.

## Public API

- `cache.get(key)` / `cache.getOrLoad(key, loader, ttl)`
- `cache.set(key, value, ttl)`
- `cache.invalidate(key | prefix | tag)`

## Events emitted

None.

## Depends on

Nothing inside the system.

## Consumed by

Any module with expensive reads (`catalog`, `schedule`, `finance-tracker`, `llm-vector`).
