# schedule-holiday

Recurring or one-off closure that prevents bookings on specific days.

## Purpose

Represent business-wide closures (public holidays, summer break) at a coarser granularity than blockers.

## Responsibilities

- Declare workspace-wide closed days.
- Support a region-sourced calendar seed for common public holidays.
- Offer opt-out per staff for staff who still work that day.

## Data model

- `schedule_holiday`: `id`, `workspace_id`, `name`, `date`, `recurrence` (`once`, `yearly`), `region?`, `applies_to_staff_ids?`, `created_at`.

## Public API

- `schedule_holiday.create(...)` / `schedule_holiday.delete(id)`
- `schedule_holiday.seed(workspace_id, region, year)` — import region defaults.
- `schedule_holiday.is_closed(workspace_id, date, staff_id?)`

## Events emitted

- `schedule_holiday.created`, `schedule_holiday.deleted`.

## Depends on

- `_workspace/schedule`, `_platform/workspace-member`.

## Consumed by

- `_workspace/schedule` (availability computation).
