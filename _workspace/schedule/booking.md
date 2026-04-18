# schedule-booking

A confirmed appointment between a client and a professional.

## Purpose

The unit that ties together client, service, staff, resource, and time. The most common entity in the workspace.

## Responsibilities

- Create bookings (full or minimal) with soft availability checks.
- Reschedule, cancel, mark no-show, mark completed.
- Trigger reminders and follow-ups via events.
- Drive invoice creation on completion.

## Data model

- `schedule_booking`: `id`, `workspace_id`, `client_id`, `service_id?`, `staff_user_id?`, `resource_id?`, `starts_at`, `ends_at`, `status`, `source`, `notes?`, `created_at`.

`status` ∈ `booked`, `pending_confirmation`, `confirmed`, `completed`, `cancelled`, `no_show`.

`source` ∈ `admin`, `self_serve`, `whatsapp_ai`, `chat_ai`, `import`.

### V0 minimum fields

For Sabriza's 3-tap flow, only these are required on create:

- `client_id` (found or created by name)
- `starts_at`

Everything else is optional at create time:

- `ends_at` — if absent, computed from service duration when `service_id` is present, else `starts_at + schedule_policy.default_duration_minutes` (default 60).
- `service_id` — nullable; Silvia asks about it later.
- `staff_user_id` — nullable for solo workspaces.
- `notes` — nullable.

## Public API

- `schedule_booking.create({ workspace_id, client_id, starts_at, service_id?, ends_at?, staff_user_id?, notes?, source })` — full-fat creation; soft-checks availability via `schedule.is_bookable`.
- `schedule_booking.create_minimal({ workspace_id, client_name | client_id, starts_at, duration_minutes?, source })` — the 3-tap path. Runs `client.find_or_create_by_name` when `client_name` is passed, then `create` with defaults filled from `schedule-policy`.
- `schedule_booking.list({ workspace_id, date_from, date_to, client_id?, staff_id?, service_id?, status? })` — powers every schedule view (list/day/week/month).
- `schedule_booking.get(id)`
- `schedule_booking.update(id, patch)` — change `service_id`, `ends_at`, `notes` without changing status; emits `booking.updated`.
- `schedule_booking.reschedule(id, new_start, new_end?)`
- `schedule_booking.cancel(id, reason?)` — `reason` is free-form; conventional values: `client_cancelled`, `pro_cancelled`, `no_response`, `declined`, `deleted` (used when the user "deletes" a booking; the row is preserved for audit and finance).
- `schedule_booking.confirm(id)` / `schedule_booking.complete(id)` / `schedule_booking.no_show(id)`

## Availability validation

Soft by default. Creation does not block on conflict; it returns a warning payload when:

- A booking overlaps another for the same staff (unless `schedule_policy.overlap_allowed`).
- Time is outside the configured `schedule` working hours.
- Time falls on a `schedule-holiday` or inside a `schedule-blocker`.

UI decides how to surface the warning. Hard blocks (past-time creation, impossible ranges) still reject.

## Events emitted

- `booking.created` — payload includes `source`.
- `booking.updated`, `booking.confirmed`, `booking.rescheduled`, `booking.cancelled`, `booking.completed`, `booking.no_show`.

## Depends on

- `_workspace/schedule`, `_workspace/schedule-policy`, `_workspace/client`.
- `_workspace/catalog-service`, `_workspace/resource`, `_workspace/client-package` (optional on create).
- `_internal/audit`, `_internal/trash`.

## Consumed by

- `_workspace/finance-invoice`, `_workspace/ai-message` (reminders), `_workspace/ai-insight` (daily snapshots), `_workspace/schedule-waitlist` (free-slot promotion).
