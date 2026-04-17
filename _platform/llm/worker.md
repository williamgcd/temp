# llm-worker

Background job runner that executes LLM inference tasks.

## Purpose

Decouple AI calls from request-response cycles: batch, retry, prioritize, and meter inference work.

## Responsibilities

- Accept inference jobs with a prompt/spec and target model.
- Enforce per-workspace and per-user rate limits.
- Retry transient failures; surface permanent failures.
- Stream partial results back to `llm-action` when the caller subscribes.

## Data model

- `llm_job`: `id`, `workspace_id`, `user_id?`, `kind` (`complete`, `embed`, `classify`, …), `model`, `input_json`, `priority`, `status` (`queued`, `running`, `done`, `failed`), `attempts`, `started_at?`, `finished_at?`, `error?`.

## Public API

- `llm_worker.enqueue(spec)` → `job_id`.
- `llm_worker.await(job_id)` — blocking helper with timeout.
- `llm_worker.cancel(job_id)`.

## Events emitted

- `llm_worker.job_started`, `llm_worker.job_finished`, `llm_worker.job_failed`.

## Depends on

- `_platform/user-quota`, `_platform/plan-subscription`.
- External LLM provider SDK.

## Consumed by

- `_platform/llm-action`, `_platform/llm-vector`.
