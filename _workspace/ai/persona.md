# ai-persona

Configurable AI assistant persona with name, tone, and behavior for a workspace.

## Purpose

Let each workspace shape how the AI sounds and behaves: its name, voice, guardrails, and allowed actions.

## Responsibilities

- Store persona definitions: identity, tone, style, refusal rules.
- Mark one persona as default per workspace; support multiple scoped personas (e.g. front-desk, concierge).
- Provide resolved system prompts to `llm-action`.

## Data model

- `ai_persona`: `id`, `workspace_id`, `name`, `scope` (`default`, `messaging`, `insight`, …), `system_prompt`, `tone`, `guardrails_json`, `is_default`, `updated_at`.

## Public API

- `ai_persona.list(workspace_id)`
- `ai_persona.upsert(...)`
- `ai_persona.resolve(workspace_id, scope)` — returns persona used for a given action scope.

## Events emitted

- `ai_persona.updated`.

## Depends on

- `_platform/workspace`, `_internal/audit`.

## Consumed by

- `_platform/llm-action` (prompt composition).
- `_workspace/ai-message`, `_workspace/ai-insight`, `_workspace/ai-action`.
