# workspace-config

Workspace-level preferences — currency, timezone, language, default timeslot.

## Purpose

Holds per-workspace operating parameters that every business module depends on for correctness (money formatting, date math, slot granularity).

## Responsibilities

- Store a known set of typed settings per workspace.
- Merge with `account-config` and plan defaults on read.
- Surface changes to subscribers so caches can warm/bust.

## Data model

- `workspace_config`: `workspace_id` (pk), `currency`, `timezone`, `language`, `timeslot_minutes`, `week_starts_on`, `business_hours_json`, `extras_json`, `updated_at`.

## Public API

- `workspace_config.get(workspace_id)` — resolved config.
- `workspace_config.patch(workspace_id, partial)`

## Events emitted

- `workspace_config.changed`.

## Depends on

- `_platform/workspace`, `_platform/account-config`, `_platform/plan`.
- `_internal/cache`, `_internal/audit`.

## Consumed by

- `_workspace/schedule` (timezone, timeslot).
- `_workspace/finance` (currency).
- Any UI rendering dates or money.
