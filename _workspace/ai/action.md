# ai-action

Automated AI-triggered actions executed in response to events.

## Purpose

Close the loop between AI and the business: the AI can not just report, but take an action — reschedule a booking, apply a promotion, send a message — subject to workspace-defined rules.

## Responsibilities

- Subscribe to triggers (event patterns) defined per workspace.
- Run an `llm-action` to decide if/what to act; validate tool-call outputs.
- Execute via the target module's public API.
- Record the decision, the execution result, and any rollback.

## Data model

- `ai_action_rule`: `id`, `workspace_id`, `trigger`, `tools_allowed`, `enabled`, `created_at`.
- `ai_action_run`: `id`, `workspace_id`, `rule_id`, `trigger_event_id`, `decision_json`, `execution_json`, `status`, `started_at`, `finished_at?`.

## Public API

- `ai_action.rule.upsert(...)` / `ai_action.rule.enable(id, bool)`
- `ai_action.dispatch(event)` — invoked by the event bus bridge.
- `ai_action.replay(run_id)`

## Events emitted

- `ai_action.decided`, `ai_action.executed`, `ai_action.failed`.

## Depends on

- `_platform/llm-action`, `_workspace/ai-setting`, `_workspace/ai-persona`.
- Every module it is allowed to call as a tool.

## Example rule kinds

- `inbound_whatsapp_booking` — parse WhatsApp request, propose slot, create booking on confirmation.
- `morning_snapshot` — send a start-of-day agenda summary via chat / push.
- `evening_summary` — send an end-of-day performance recap.
- `empty_day_alert` — when a low-utilization day is approaching, offer to message frequent clients.
- `no_show_followup` — when `booking.no_show` fires, draft a follow-up message.
- `post_appointment_review` — request a review N hours after `booking.completed`.

## Consumed by

- No module — it's a leaf consumer that closes event loops.
