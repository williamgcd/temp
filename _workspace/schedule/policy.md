# schedule-policy

Bookability rules and defaults for the workspace's schedule.

> **Settings UI**: lives at "Agenda > Regras". Per-service overrides at "Catálogo > Serviço > Regras".

## Purpose

Separate **"what is allowed"** (policies — overlap, windows, fees, buffers, slot granularity, default duration) from **"what is bookable"** (`schedule` — availability derived from hours/blockers/holidays). Policies change often as Sabriza learns her business; isolating them keeps audit and overrides clean.

## Responsibilities

- Store workspace-level policy defaults.
- Accept per-service overrides (one `catalog-service` can override any subset).
- Expose a resolver that merges defaults with overrides for a given `service_id`.
- Feed `schedule-booking` validation and UI warnings.
- Feed `ai-message` reminder defaults.

## Data model

- `schedule_policy`: `workspace_id` (pk), `overlap_allowed`, `slot_granularity_minutes`, `default_duration_minutes`, `buffer_before_minutes`, `buffer_after_minutes`, `advance_booking_min_hours?`, `advance_booking_max_days?`, `reschedule_window_hours?`, `reschedule_fee_cents?`, `cancellation_window_hours?`, `cancellation_fee_cents?`, `no_show_grace_minutes`, `no_show_fee_cents?`, `deposit_required`, `deposit_percent?`, `reminder_enabled`, `reminder_hours_before`, `updated_at`.
- `schedule_policy_override`: `workspace_id`, `service_id` (pk together), partial subset of the fields above, `updated_at`.

### V1 defaults (applied at workspace creation)

| Field | Default |
|---|---|
| `overlap_allowed` | `true` |
| `slot_granularity_minutes` | `30` |
| `default_duration_minutes` | `60` |
| `buffer_before_minutes` | `0` |
| `buffer_after_minutes` | `0` |
| `advance_booking_min_hours` | `null` (no minimum) |
| `advance_booking_max_days` | `null` (no maximum) |
| `reschedule_window_hours` | `null` |
| `reschedule_fee_cents` | `0` |
| `cancellation_window_hours` | `null` |
| `cancellation_fee_cents` | `0` |
| `no_show_grace_minutes` | `30` |
| `no_show_fee_cents` | `0` |
| `deposit_required` | `false` |
| `deposit_percent` | `null` |
| `reminder_enabled` | `true` |
| `reminder_hours_before` | `24` |

These defaults intentionally do not constrain Sabriza. She can book anywhere, overlap anything, no fees. Silvia suggests tightening based on observed patterns (e.g. after the 3rd no-show, "*quer definir multa de no-show?*").

## Public API

- `schedule_policy.get(workspace_id)` — resolved defaults.
- `schedule_policy.patch(workspace_id, partial)`
- `schedule_policy.get_resolved({ workspace_id, service_id? })` — merges defaults with per-service override.
- `schedule_policy.set_override(workspace_id, service_id, partial)` / `schedule_policy.clear_override(workspace_id, service_id)`

## Events emitted

- `schedule_policy.changed`, `schedule_policy.override_changed`.

## Depends on

- `_platform/workspace`, `_platform/workspace-config`.
- `_internal/cache`, `_internal/audit`.

## Consumed by

- `_workspace/schedule` — availability math.
- `_workspace/schedule-booking` — validation and defaults on create.
- `_workspace/ai-message` — reminder defaults.
- `_workspace/ai-action` — rules that reference policy (e.g. auto-cancel on no-show window).
- Settings UI: "Agenda > Regras" (workspace), "Serviço > Regras" (override).
