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

### Inbound

- `inbound_whatsapp_booking` — parse a WhatsApp request, propose a slot, create booking on confirmation. Includes urgency detection (encaixe).
- `inbound_whatsapp_reschedule` — client asks to move; Silvia proposes new slot and reschedules.
- `inbound_whatsapp_cancel` — client cancels; Silvia processes, applies fee if policy says so (with confirmation).

### Time-based

- `morning_snapshot` — start-of-day agenda summary via chat / push.
- `evening_summary` — end-of-day performance recap.
- `empty_day_alert` — low-utilization day approaching; offer to message frequent clients.
- `year_start_holiday_seed` — January: propose seeding national holidays.

### Detection on bookings

- `no_show_detection` — booking past `ends_at + no_show_grace_minutes` with no interaction → propose `no_show`.
- `auto_complete_suggestion` — booking past `ends_at + 30min` still `booked`/`confirmed` → propose `complete`.
- `no_show_followup` — when `booking.no_show` fires, draft a follow-up message.
- `post_appointment_review` — request a review N hours after `booking.completed`.

### Pattern detection / suggestions

- `pattern_detect_working_hours` — propose default hours after observing N consistent bookings.
- `hours_change_conflict_alert` — when working hours change, list affected bookings and ask what to do.
- `suggest_no_show_fee` — repeated no-shows → propose enabling `no_show_fee_cents`.
- `suggest_min_advance_window` — short-notice surprises → propose `advance_booking_min_hours`.
- `suggest_reschedule_policy` — frequent reschedules → propose `reschedule_window_hours` + fee.
- `suggest_reminder_timing` — observed correlation between lead time and show-rate → propose changing `reminder_hours_before`.

## Consumed by

- No module — it's a leaf consumer that closes event loops.
