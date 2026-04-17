# llm

The LLM runtime. This folder is a logical grouping: there is no bare `llm` module. It contains the three modules every AI feature in the product depends on.

## Modules

| Module | Definition |
|---|---|
| [llm-vector](./vector.md) | Vector embeddings stored for semantic search over workspace data. |
| [llm-worker](./worker.md) | Background job runner that executes LLM inference tasks. |
| [llm-action](./action.md) | Structured LLM-powered action dispatched and tracked by the platform. |

## Rules

- Business-domain AI modules (`_workspace/ai-*`) never call the model provider directly — they go through `llm-action`.
- Usage is metered against `plan-subscription` limits and `user-quota`.
- Prompts and outputs are persisted on `llm-action` for audit and replay.
