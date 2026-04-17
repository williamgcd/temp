# catalog-service

Service offered by a professional, listed in the catalog.

## Purpose

A bookable unit of time + expertise: haircut, consultation, personal training session, therapy hour.

## Responsibilities

- Declare duration, buffer time, required resources, eligible staff.
- Expose bookability to the schedule module.
- Support variants (e.g. short/long haircut) as related services.

## Data model

- `catalog_service`: `catalog_item_id` (pk), `duration_minutes`, `buffer_before_minutes`, `buffer_after_minutes`, `eligible_staff_ids[]`, `required_resource_types[]`.

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
