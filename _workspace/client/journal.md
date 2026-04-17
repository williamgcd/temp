# client-journal

Timestamped notes and observations recorded on a client.

## Purpose

Longitudinal record of what happened with a client over time — session notes, measurements, photos, follow-up reminders.

## Responsibilities

- Append-only entries with author, timestamp, kind, body, attachments.
- Scope entries to a booking when relevant.
- Power retrieval for AI context and care continuity.

## Data model

- `client_journal`: `id`, `workspace_id`, `client_id`, `author_user_id`, `booking_id?`, `kind` (`note`, `measurement`, `photo`, `reminder`), `body_md`, `data_json?`, `attachments_json?`, `created_at`.

## Public API

- `client_journal.append(...)`
- `client_journal.list({ client_id, kind?, since?, until? })`
- `client_journal.for_booking(booking_id)`

## Events emitted

- `client_journal.appended`.

## Depends on

- `_workspace/client`, `_workspace/document` (attachments).
- `_platform/llm-vector` (indexing for AI retrieval).

## Consumed by

- `_workspace/ai-insight`, `_workspace/ai-message` (context).
