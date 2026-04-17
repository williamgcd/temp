# trash

Soft-delete bin with auto-purge for recoverable record deletion.

## Purpose

Centralize deletion across the system so that every module gets undo, retention windows, and consistent hard-delete behavior without reimplementing it.

## Responsibilities

- Move records from their owning module into a serialized trash entry.
- Expose restore and purge APIs; enforce retention TTL.
- Worker-driven hard-delete of expired entries.
- Prevent restore when restoring would violate current constraints (e.g. referenced workspace gone).

## Data model

- `trash_entry`: `id`, `workspace_id?`, `source_module`, `source_id`, `payload_json`, `deleted_by`, `deleted_at`, `purge_after`.

## Public API

- `trash.soft_delete(module, id, actor)`
- `trash.restore(entry_id)`
- `trash.purge(entry_id)` — immediate hard delete.
- `trash.list({ workspace_id, module? })`

## Events emitted

- `trash.item_deleted`, `trash.item_restored`, `trash.item_purged`.

## Depends on

Nothing inside the system.

## Consumed by

Every module that supports deletion. Modules call `trash.soft_delete` rather than issuing their own DELETE.
