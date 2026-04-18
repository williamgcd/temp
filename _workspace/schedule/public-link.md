# schedule-public-link

Public-facing link and form for clients to request bookings without an account.

## Purpose

Expose workspace availability and a booking-request form on a public URL. Used in Instagram bios, link-in-bio pages, and shared via WhatsApp. Bookings created here land in `pending_confirmation` for the workspace owner to approve.

## Responsibilities

- Manage the workspace's public booking link (slug, on/off, intro text).
- Filter which `catalog-service` items are visible publicly.
- Wrap `schedule.available_slots` and `schedule_booking.create` with public-safe defaults.
- Rate-limit and validate inbound requests.

## Data model

- `schedule_public_link`: `workspace_id` (pk), `slug` (unique global), `enabled`, `services_visible: string[] | null`, `intro_text?`, `updated_at`.
- One link per workspace v1. `slug` is workspace-chosen, suggested from the workspace name; must be globally unique.
- `services_visible = null` ⇒ every `catalog-service` with `visibility = public` is shown; an explicit list narrows it further.

## Public API

### Workspace-side

- `schedule_public_link.enable({ workspace_id, slug })` / `schedule_public_link.disable(workspace_id)`
- `schedule_public_link.update({ workspace_id, services_visible?, intro_text? })`
- `schedule_public_link.suggest_slug({ workspace_id })` — slug suggestion based on name.

### Public-facing (no auth)

- `schedule_public_link.get_by_slug(slug)` — workspace card payload.
- `schedule_public_link.available_slots({ slug, service_slug?, date_range })` — wraps `schedule.available_slots`.
- `schedule_public_link.request_booking({ slug, service_slug, starts_at, client_name, client_phone, notes? })` — creates a `schedule_booking` with `status = pending_confirmation`, `source = self_serve`. Rate-limited per IP and per phone.

## Events emitted

- `schedule_public_link.enabled`, `schedule_public_link.disabled`, `schedule_public_link.updated`, `schedule_public_link.booking_requested`.

## Depends on

- `_workspace/schedule`, `_workspace/schedule-booking`, `_workspace/catalog-service`, `_workspace/client`.
- `_internal/audit`, `_internal/cache` (slot lookup).

## Consumed by

- Public HTTP edge (the public site at `/b/{slug}`).
- Workspace settings UI ("Agenda > Link de agendamento").
- `_workspace/ai-action` (notifies Sabriza when a request comes in).
