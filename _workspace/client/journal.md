# client-journal

Timestamped notes and observations recorded on a client.

## Purpose

Longitudinal record of what happened with a client over time — session notes, measurements, photos, follow-up reminders.

## Responsibilities

- Append entries with author, timestamp, kind, body, attachments.
- Allow edit and soft-delete (via `_internal/trash`).
- Scope entries to a booking when relevant.
- Power retrieval for AI context and care continuity.
- Fire reminders when an entry of `kind=reminder` reaches its `fire_at`.

## Data model

- `client_journal`: `id`, `workspace_id`, `client_id`, `author_user_id`, `booking_id?`, `kind`, `body_md`, `data_json?`, `attachments_json?`, `created_at`, `updated_at?`.

`kind` ∈ `note`, `photo`, `measurement`, `reminder`.

`data_json` shape per kind:

- `note` — usually empty; body lives in `body_md`.
- `photo` — empty; image refs live in `attachments_json`.
- `measurement` — `{ items: [{ label, value, unit? }] }`. Free-form labels.
- `reminder` — `{ fire_at, title, action_hint? }`. `fire_at` drives the scheduled event.

## Public API

- `client_journal.append({ workspace_id, client_id, kind, body_md, data_json?, attachments_json?, booking_id? })`
- `client_journal.update(id, patch)` — edit body/attachments; not used for status changes.
- `client_journal.delete(id)` — soft via `_internal/trash`.
- `client_journal.list({ workspace_id, client_id, kind?, since?, until? })`
- `client_journal.for_booking(booking_id)`

## Events emitted

- `client_journal.appended`, `client_journal.updated`, `client_journal.deleted`.
- `client_journal.reminder_due` — fired when an entry of `kind=reminder` reaches its `fire_at`.

## Depends on

- `_workspace/client`, `_workspace/document` (attachments).
- `_platform/llm-vector` (indexing for AI retrieval).
- `_internal/trash`.

## Consumed by

- `_workspace/ai-insight`, `_workspace/ai-message` (context for replies and summaries).
- `_workspace/ai-action` (`journal_reminder_fire` rule listens to `reminder_due`).
