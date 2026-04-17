# ai (workspace)

Workspace-facing AI modules. This folder is a logical grouping — there is no bare `ai` module. The modules here configure the AI for a workspace and compose the platform's `_platform/llm-action` with workspace-specific context.

## Modules

| Module | Definition |
|---|---|
| [ai-persona](./persona.md) | Configurable AI assistant persona with name, tone, and behavior for a workspace. |
| [ai-setting](./setting.md) | Workspace-level AI feature configuration and toggles. |
| [ai-example](./example.md) | Few-shot examples used to guide AI outputs. |
| [ai-insight](./insight.md) | AI-generated analytical summaries and observations from workspace data. |
| [ai-message](./message.md) | AI-composed messages sent to or on behalf of clients. |
| [ai-action](./action.md) | Automated AI-triggered actions executed in response to events. |

## Rules

- No module here calls a model provider directly — always via `_platform/llm-action`.
- `ai-setting` gates whether any given feature runs; `plan-subscription` gates whether it is available at all.
- All meaningful inputs and outputs are persisted (via `llm-action`) for audit and replay.
