# schedule-booking

A confirmed appointment between a client and a professional.

## Purpose

The unit that ties together client, service, staff, resource, and time. The most common entity in the workspace.

## Responsibilities

- Create bookings with conflict/availability checks.
- Reschedule, cancel, mark no-show, mark completed.
- Trigger reminders and follow-ups via events.
- Drive invoice creation on completion.

## Data model

- `schedule_booking`: `id`, `workspace_id`, `client_id`, `service_id`, `staff_user_id`, `resource_id?`, `starts_at`, `ends_at`, `status` (`booked`, `confirmed`, `completed`, `cancelled`, `no_show`), `source` (`admin`, `self_serve`, `import`), `notes?`, `created_at`.

## Public API

- `schedule_booking.create({ ... })` — validates via `schedule.is_bookable`.
- `schedule_booking.reschedule(id, new_start)`
- `schedule_booking.cancel(id, reason)` / `schedule_booking.complete(id)` / `schedule_booking.no_show(id)`

## Events emitted

- `booking.created`, `booking.confirmed`, `booking.rescheduled`, `booking.cancelled`, `booking.completed`, `booking.no_show`.

## Depends on

- `_workspace/schedule`, `_workspace/client`, `_workspace/catalog-service`, `_workspace/resource`, `_workspace/client-package`.
- `_internal/audit`, `_internal/trash`.

## Consumed by

- `_workspace/finance-invoice`, `_workspace/ai-message` (reminders), `_workspace/schedule-waitlist` (free-slot promotion).
