# ai-message

AI-composed messages sent to or on behalf of clients.

## Purpose

Draft and (optionally) auto-send outbound messages to clients — appointment reminders, follow-ups, replies to inbound questions.

## Responsibilities

- Draft a message given a template intent plus client context.
- Support review-before-send, auto-send, and scheduled-send modes.
- Deliver via the appropriate channel (email/SMS/chat) and record status.
- Learn from user edits via `ai-example`.

## Data model

- `ai_message`: `id`, `workspace_id`, `client_id`, `channel`, `intent`, `draft_body`, `final_body?`, `status` (`drafted`, `queued`, `sent`, `failed`, `cancelled`), `sent_at?`.

## Public API

- `ai_message.draft({ workspace_id, client_id, intent, context? })`
- `ai_message.send(id)` / `ai_message.cancel(id)`
- `ai_message.auto_reply({ workspace_id, inbound_message })` — used by inbound webhooks.

## Events emitted

- `ai_message.drafted`, `ai_message.sent`, `ai_message.failed`.

## Depends on

- `_platform/llm-action`, `_workspace/ai-persona`, `_workspace/ai-setting`.
- `_workspace/client`, external messaging provider.

## Consumed by

- `_workspace/mkt-campaign` (drafting campaign copy).
- `_workspace/ai-action` (send-message actions).
