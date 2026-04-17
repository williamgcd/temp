# audit

Immutable log of who did what and when, across every module.

## Purpose

Provide a tamper-evident, append-only trail of meaningful state changes for compliance, debugging, and admin review.

## Responsibilities

- Subscribe to domain events on the bus and persist them as audit entries.
- Normalize actor (user, system, AI) and target (module, entity id) into a consistent shape.
- Retain entries per retention policy; never mutate or delete individual rows.
- Offer read-only query API for admin tooling.

## Data model

- `audit_entry`: `id`, `workspace_id?`, `account_id?`, `actor_type`, `actor_id`, `action`, `target_module`, `target_id`, `payload_json`, `ip`, `user_agent`, `occurred_at`.

## Public API

- `audit.query({ workspace_id, actor_id?, target?, since?, until? })`
- `audit.stream(workspace_id)` — admin-only live tail.

## Events emitted

None. Audit is a sink, not a source.

## Depends on

- `_internal/cache` (for query hot paths).

## Consumed by

- Admin dashboards across `_platform` and `_workspace`.
