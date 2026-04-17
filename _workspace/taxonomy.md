# taxonomy

Hierarchical category and tag system for classifying catalog items and clients.

## Purpose

One place to define how a workspace slices its world — categories for catalog, tags for clients, labels for anything else.

## Responsibilities

- Define a namespace (e.g. `catalog.category`, `client.tag`) and allowed attachment targets.
- Support hierarchy within a namespace (tree of nodes).
- Attach zero-to-many taxonomy nodes to any target entity.
- Rename/merge/split nodes without breaking attachments.

## Data model

- `taxonomy_namespace`: `workspace_id`, `key`, `hierarchical`, `applies_to[]`.
- `taxonomy_node`: `id`, `workspace_id`, `namespace_key`, `parent_id?`, `name`, `slug`, `order`.
- `taxonomy_attachment`: `id`, `workspace_id`, `node_id`, `target_type`, `target_id`, `created_at`.

## Public API

- `taxonomy.namespace.list(workspace_id)`
- `taxonomy.node.create(...)` / `taxonomy.node.move(id, new_parent_id?)` / `taxonomy.node.merge(from_id, into_id)`
- `taxonomy.attach(node_id, target_type, target_id)` / `taxonomy.detach(...)`
- `taxonomy.nodes_for(target_type, target_id)`

## Events emitted

- `taxonomy.node_created`, `taxonomy.node_moved`, `taxonomy.node_merged`, `taxonomy.attached`, `taxonomy.detached`.

## Depends on

- `_platform/workspace`, `_internal/audit`.

## Consumed by

- `_workspace/catalog`, `_workspace/client`, `_workspace/mkt-campaign` (segments), `_workspace/mkt-promotion` (scopes).
