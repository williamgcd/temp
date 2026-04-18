# catalog-service

Service offered by a professional, listed in the catalog.

## Purpose

A bookable unit of time + expertise: haircut, consultation, personal training session, therapy hour.

## Responsibilities

- Declare duration, buffer time, required resources, eligible staff.
- Expose bookability to the schedule module.
- Support variants (e.g. short/long haircut) as related services.

## Data model

- `catalog_service`: `catalog_item_id` (pk), `duration_minutes`, `buffer_before_minutes`, `buffer_after_minutes`, `visibility` (`public`, `private`; default `public`), `eligible_staff_ids[]`, `required_resource_types[]`.

`visibility = public` exposes the service on the public booking link (`schedule-public-link`). `private` hides it from clients (admin can still book it).

A service may override any field of `_workspace/schedule-policy` via `schedule_policy.set_override(workspace_id, service_id, partial)`.

## Public API

- `catalog_service.create(...)` / `catalog_service.update(...)`
- `catalog_service.eligible_staff(id)`
- `catalog_service.duration_total(id)` — including buffers.

## Events emitted

- `catalog_service.created`, `catalog_service.updated`.

## Depends on

- `_workspace/catalog`, `_workspace/resource`.
- `_platform/workspace-member` (eligible staff validation).

## Consumed by

- `_workspace/schedule`, `_workspace/schedule-booking`.
