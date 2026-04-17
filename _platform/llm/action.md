# llm-action

Structured LLM-powered action dispatched and tracked by the platform.

## Purpose

Single entry point for every AI feature: wraps prompt construction, tool calling, result parsing, and persistence.

## Responsibilities

- Accept a typed action spec (input shape, output shape, tools available).
- Build the prompt (persona, examples, retrieved context).
- Delegate inference to `llm-worker`; loop on tool calls.
- Persist the full transcript for replay and audit.

## Data model

- `llm_action`: `id`, `workspace_id`, `user_id?`, `action_type`, `input_json`, `output_json?`, `transcript_json`, `status`, `started_at`, `finished_at?`, `cost_usd?`.

## Public API

- `llm_action.run({ workspace_id, action_type, input })` → typed output.
- `llm_action.get(id)` — retrieve the transcript.
- `llm_action.replay(id, overrides?)` — re-run with the same inputs.

## Events emitted

- `llm_action.started`, `llm_action.finished`, `llm_action.failed`.

## Depends on

- `_platform/llm-worker`, `_platform/llm-vector`.
- `_workspace/ai-persona`, `_workspace/ai-example`, `_workspace/ai-setting` (context).
- `_internal/audit`.

## Consumed by

- Every `_workspace/ai-*` module.
