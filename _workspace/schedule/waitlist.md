# schedule-waitlist

Queue of clients waiting for an opening in a full schedule slot.

## Purpose

Capture demand when preferred slots are full and auto-offer the slot when it opens up.

## Responsibilities

- Accept a waitlist entry: client + service + desired window + priority.
- Listen for cancellations in matching windows.
- Offer the freed slot to the next eligible entry for a timeboxed window.
- Escalate to the next entry if the offer isn't accepted.

## Data model

- `schedule_waitlist`: `id`, `workspace_id`, `client_id`, `service_id`, `preferred_staff_id?`, `window_start`, `window_end`, `priority`, `status` (`waiting`, `offered`, `accepted`, `expired`, `cancelled`), `offered_at?`, `expires_at?`.

## Public API

- `schedule_waitlist.join(...)` / `schedule_waitlist.cancel(id)`
- `schedule_waitlist.offer(entry_id, slot)` — internal, called on cancellation.
- `schedule_waitlist.accept(entry_id)` → booking.

## Events emitted

- `waitlist.joined`, `waitlist.offered`, `waitlist.accepted`, `waitlist.expired`.

## Depends on

- `_workspace/schedule`, `_workspace/client`, `_workspace/catalog-service`.
- `_workspace/ai-message` (offer notifications).

## Consumed by

- `_workspace/schedule-booking` (accept → create booking).
