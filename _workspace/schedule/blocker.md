# schedule-blocker

Time block that marks a period as unavailable for new bookings.

## Purpose

Carve out time for breaks, personal appointments, equipment downtime, or training — anything that makes a slot non-bookable without creating a client booking.

## Responsibilities

- Attach blockers to a staff member, resource, or the whole workspace.
- Support recurring blockers (weekly lunch) and one-off blockers.
- Feed the `schedule.available_slots` computation.

## Data model

- `schedule_blocker`: `id`, `workspace_id`, `scope` (`workspace`, `staff`, `resource`), `scope_id?`, `reason`, `starts_at`, `ends_at`, `rrule?`, `created_by`, `created_at`.

`reason` is a free-form string. Common presets surfaced in UI as suggestions: `almoço`, `consulta`, `pessoal`, `manutenção`, `folga`, `evento`. Not an enum — Sabriza can type anything.

## Public API

- `schedule_blocker.create(...)` / `schedule_blocker.update(id, patch)` / `schedule_blocker.delete(id)`
- `schedule_blocker.list({ workspace_id, scope?, date_range })`

## Events emitted

- `schedule_blocker.created`, `schedule_blocker.updated`, `schedule_blocker.deleted`.

## Depends on

- `_workspace/schedule`, `_workspace/resource`, `_platform/workspace-member`.

## Consumed by

- `_workspace/schedule` (availability computation).
