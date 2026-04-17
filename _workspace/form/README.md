# form

Custom form definition attached to a workflow or intake process.

## Purpose

Capture structured input from clients or staff — intake forms, health questionnaires, satisfaction surveys, consent forms.

## Responsibilities

- Define a form with ordered questions and conditional logic.
- Version forms so historical responses stay interpretable.
- Attach forms to triggers (booking type, onboarding, post-visit).
- Expose rendering metadata for the form UI.

## Data model

- `form`: `id`, `workspace_id`, `name`, `version`, `status` (`draft`, `active`, `archived`), `triggers_json`, `settings_json`, `created_at`.

## Public API

- `form.create(...)` / `form.publish(id)` — creates an active version.
- `form.render(id)` — full question tree.
- `form.attach(form_id, trigger)` / `form.detach(form_id, trigger)`

## Events emitted

- `form.published`, `form.attached`, `form.archived`.

## Depends on

- `_internal/audit`, `_internal/trash`.

## Consumed by

- `_workspace/schedule-booking` (intake), `_workspace/client` (profile fields), `_workspace/ai-action` (post-visit follow-ups).

## Submodules

- [form-question](./question.md)
- [form-response](./response.md)
