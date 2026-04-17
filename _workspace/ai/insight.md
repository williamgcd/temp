# ai-insight

AI-generated analytical summaries and observations from workspace data.

## Purpose

Surface non-obvious findings to the operator: revenue trends, churn signals, underperforming services, busy hours.

## Responsibilities

- Schedule insight generation runs (daily/weekly or on-demand).
- Aggregate workspace data and pass it to `llm-action` with an `insight` action type.
- Persist insights with a confidence score and supporting evidence.
- Dismiss or pin insights per user.

## Data model

- `ai_insight`: `id`, `workspace_id`, `topic`, `title`, `body_md`, `evidence_json`, `confidence`, `status` (`new`, `read`, `dismissed`, `pinned`), `generated_at`.

## Public API

- `ai_insight.generate({ workspace_id, topic? })`
- `ai_insight.list({ workspace_id, status? })`
- `ai_insight.update_status(id, status)`

## Events emitted

- `ai_insight.generated`, `ai_insight.dismissed`, `ai_insight.pinned`.

## Depends on

- `_platform/llm-action`, `_platform/llm-vector`.
- `_workspace/finance-tracker`, `_workspace/schedule-booking`, `_workspace/client` (sources).

## Consumed by

- Dashboard UI.
