# ai-example

Few-shot examples used to guide AI outputs.

## Purpose

Let each workspace teach the AI by example — preferred replies, ideal summaries, corrections on bad outputs.

## Responsibilities

- Store input/output pairs tagged by action type and scope.
- Rank and sample the most relevant examples at prompt time.
- Capture user corrections as new examples (feedback loop).

## Data model

- `ai_example`: `id`, `workspace_id`, `action_type`, `scope?`, `input_json`, `output_json`, `quality`, `source` (`manual`, `correction`, `seed`), `created_by`, `created_at`.

## Public API

- `ai_example.add(...)`
- `ai_example.list({ workspace_id, action_type, scope? })`
- `ai_example.sample({ workspace_id, action_type, k })` — returns top-k for prompt injection.

## Events emitted

- `ai_example.added`, `ai_example.removed`.

## Depends on

- `_platform/workspace`.
- `_platform/llm-vector` (relevance ranking).

## Consumed by

- `_platform/llm-action` at prompt build time.
