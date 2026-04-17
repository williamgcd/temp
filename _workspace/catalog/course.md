# catalog-course

Multi-session or structured course offering in the catalog.

## Purpose

A series of sessions sold as a single unit: 10-class yoga pass, 6-week bootcamp, semester-long program.

## Responsibilities

- Declare total session count, cadence, and session template (service + duration).
- Track enrollment and progress at the client level.
- Generate the per-session bookings on enrollment (cohort courses) or on-demand (drop-in courses).

## Data model

- `catalog_course`: `catalog_item_id` (pk), `kind` (`cohort`, `drop_in`), `session_count`, `session_template_json`, `starts_at?`, `ends_at?`.

## Public API

- `catalog_course.create(...)` / `catalog_course.update(...)`
- `catalog_course.enroll({ course_id, client_id })`
- `catalog_course.progress(course_id, client_id)`

## Events emitted

- `catalog_course.created`, `catalog_course.enrolled`, `catalog_course.completed`.

## Depends on

- `_workspace/catalog`, `_workspace/catalog-service`.
- `_workspace/schedule-booking` (session materialization).

## Consumed by

- `_workspace/client-package`, `_workspace/finance-invoice`.
