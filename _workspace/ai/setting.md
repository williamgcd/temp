# ai-setting

Workspace-level AI feature configuration and toggles.

## Purpose

Centralize the on/off switches and tuning knobs for every AI feature a workspace can use.

## Responsibilities

- Store per-feature toggles (auto-reply, auto-insight, auto-action).
- Store tuning values (creativity, reply length, max daily auto-sends).
- Respect plan-level feature gating from `plan-subscription`.

## Data model

- `ai_setting`: `workspace_id` (pk), `features_json`, `tuning_json`, `updated_by`, `updated_at`.

## Public API

- `ai_setting.get(workspace_id)` — resolved (plan → workspace override).
- `ai_setting.patch(workspace_id, partial)`
- `ai_setting.enabled(workspace_id, feature)` — boolean helper.

## Events emitted

- `ai_setting.changed`.

## Depends on

- `_platform/workspace`, `_platform/plan-subscription`.
- `_internal/cache`, `_internal/audit`.

## Consumed by

- Every other `ai-*` module gates on this.
- `_platform/llm-action` reads tuning to set model parameters.
