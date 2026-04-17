# track

Event tracking for user and system actions, used in product analytics.

## Purpose

Record behavioral events (page views, feature usage, funnel steps) separately from the durable audit log, optimized for aggregation.

## Responsibilities

- Accept event batches from clients and servers.
- Enrich with workspace, user, and session context.
- Forward to the analytics pipeline (warehouse or external provider).
- Debounce / dedupe client-side retries.

## Data model

- `track_event`: `id`, `workspace_id?`, `user_id?`, `session_id`, `name`, `properties_json`, `occurred_at`, `received_at`.

## Public API

- `track.event(name, props, context)`
- `track.batch(events[])`

## Events emitted

None on the system event bus (emissions go to the analytics sink).

## Depends on

Nothing inside the system.

## Consumed by

- Every client-facing module that records product analytics.
- Not to be confused with `audit` (compliance) or the event bus (inter-module messaging).
