# resource

Physical or virtual asset (room, equipment, device) that can be reserved alongside a booking.

## Purpose

Represent scarce shared assets so bookings correctly claim a room or device and prevent double-booking.

## Responsibilities

- Declare resources with a type and per-resource working hours.
- Participate in `schedule.available_slots` (resource must be free).
- Attach to a service requirement so the booking flow auto-selects the right resource.
- Support maintenance blockers via `schedule-blocker`.

## Data model

- `resource`: `id`, `workspace_id`, `type` (`room`, `equipment`, `device`, custom), `name`, `location?`, `capacity`, `working_hours_json?`, `status` (`active`, `retired`), `created_at`.

## Public API

- `resource.create(...)` / `resource.update(id, patch)` / `resource.retire(id)`
- `resource.find_available({ workspace_id, type, window, required_capacity? })`

## Events emitted

- `resource.created`, `resource.updated`, `resource.retired`.

## Depends on

- `_platform/workspace`, `_internal/audit`, `_internal/trash`.

## Consumed by

- `_workspace/schedule`, `_workspace/schedule-booking`, `_workspace/catalog-service` (required resource types).
