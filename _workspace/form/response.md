# form-response

A client's submitted answers to a form.

## Purpose

Persist the answers to a form instance, pinned to the form version that was answered.

## Responsibilities

- Create a response record tied to a client and (optionally) a booking.
- Validate answers against the form version's questions.
- Persist answers keyed by question `key` for stable analytics.
- Expose read-only rendering for staff review.

## Data model

- `form_response`: `id`, `workspace_id`, `form_id`, `form_version`, `client_id`, `booking_id?`, `answers_json`, `submitted_at`, `submitted_by_user_id?`.

## Public API

- `form_response.submit({ form_id, client_id, answers, booking_id? })`
- `form_response.get(id)` / `form_response.list({ client_id? | form_id? | booking_id? })`
- `form_response.answer(response_id, key)` — read a specific answer.

## Events emitted

- `form_response.submitted`.

## Depends on

- `_workspace/form`, `_workspace/form-question`, `_workspace/client`, `_workspace/schedule-booking`.
- `_platform/llm-vector` (optional indexing).

## Consumed by

- `_workspace/ai-insight`, `_workspace/client-journal`.
