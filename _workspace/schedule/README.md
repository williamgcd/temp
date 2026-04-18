# schedule

Core availability and calendar configuration for a workspace or professional.

## Purpose

Answer "is this slot bookable, for whom, with what resource?" — the authoritative source of workspace time.

## Responsibilities

- Define working hours per staff and per location.
- Combine holidays, blockers, existing bookings, buffers, and policy into a consolidated availability view.
- Expose slot proposal and conflict detection APIs used by booking flows.

## Data model

- `schedule`: `id`, `workspace_id`, `owner_type` (`workspace`, `staff`, `location`), `owner_id`, `working_hours_json`, `timezone`, `updated_at`.

## Public API

- `schedule.available_slots({ workspace_id, service_id?, staff_id?, resource_id?, date_range })`
- `schedule.is_bookable({ staff_id?, start, end, resource_id? })` — returns `{ ok, warnings[] }`. Hard rejects only for impossible ranges; overlaps/out-of-hours are warnings honoring `schedule-policy`.
- `schedule.update_hours(owner_type, owner_id, working_hours)`

## Events emitted

- `schedule.hours_updated`.

## Depends on

- `_platform/workspace-config` (timezone, timeslot).
- `_workspace/schedule-policy`, `_workspace/catalog-service`, `_workspace/resource`.
- `_platform/workspace-member` (staff).

## Consumed by

- `_workspace/schedule-booking`, `_workspace/schedule-waitlist`, booking UI.

## Submodules

- [schedule-booking](./booking.md)
- [schedule-policy](./policy.md)
- [schedule-waitlist](./waitlist.md)
- [schedule-blocker](./blocker.md)
- [schedule-holiday](./holiday.md)
