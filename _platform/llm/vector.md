# llm-vector

Vector embeddings stored for semantic search over workspace data.

## Purpose

Provide a single vector store used by every AI feature to retrieve relevant context: clients, journals, catalog, documents.

## Responsibilities

- Embed text with a chosen model and store the vector with source metadata.
- Support upsert, delete, and filtered nearest-neighbor search.
- Re-embed on source change; purge on source delete.

## Data model

- `llm_vector`: `id`, `workspace_id`, `source_module`, `source_id`, `chunk_index`, `embedding`, `content`, `metadata_json`, `model`, `created_at`.

## Public API

- `llm_vector.upsert({ workspace_id, source_module, source_id, content, metadata? })`
- `llm_vector.delete({ source_module, source_id })`
- `llm_vector.search({ workspace_id, query, filter?, top_k })`

## Events emitted

- `llm_vector.upserted`, `llm_vector.deleted`.

## Depends on

- `_platform/llm-worker` (async re-embedding).
- `_internal/cache`.

## Consumed by

- `_workspace/ai-insight`, `_workspace/ai-message`, `_workspace/ai-action`.
