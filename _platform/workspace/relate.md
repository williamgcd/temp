# workspace-relate

Relationships or links between workspaces — franchises, branches, parent/child.

## Purpose

Express that one workspace is related to another (e.g. a brand headquarters and its branches) for reporting, shared catalogs, and cross-workspace analytics.

## Responsibilities

- Declare a typed directed relationship between two workspaces in the same account.
- Enforce allowed relationship types and cycle prevention for `parent` type.
- Expose traversal helpers (descendants, siblings).

## Data model

- `workspace_relate`: `id`, `account_id`, `from_workspace_id`, `to_workspace_id`, `kind` (`parent`, `branch`, `partner`, custom), `metadata_json`, `created_at`.

## Public API

- `workspace_relate.link({ from, to, kind })`
- `workspace_relate.unlink(id)`
- `workspace_relate.children(workspace_id)` / `workspace_relate.parent(workspace_id)`

## Events emitted

- `workspace_relate.linked`, `workspace_relate.unlinked`.

## Depends on

- `_platform/workspace`, `_platform/account`.

## Consumed by

- Cross-workspace reporting dashboards.
- `_workspace/catalog` when sharing catalogs across branches.
