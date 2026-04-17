# workspace

Isolated tenant environment with its own data, members, and settings.

## Purpose

A workspace is the unit of isolation every business-domain row is scoped to. Members belong to workspaces, not to the account directly.

## Responsibilities

- Create workspaces under an account; enforce plan quotas.
- Lifecycle: active, archived, deleted.
- Coordinate seeding of default data on creation.
- Expose workspace-scoped context used by every `_workspace` module.

## Data model

- `workspace`: `id`, `account_id`, `name`, `slug`, `status`, `created_by`, `archived_at?`, `created_at`.

## Public API

- `workspace.create({ account_id, name, created_by })`
- `workspace.rename(id, name)`
- `workspace.archive(id)` / `workspace.restore(id)`
- `workspace.context(id)` — returns config + member cache used by request pipeline.

## Events emitted

- `workspace.created`, `workspace.renamed`, `workspace.archived`, `workspace.restored`, `workspace.deleted`.

## Depends on

- `_platform/account`, `_platform/plan-subscription` (quota check).
- `_platform/workspace-seeder` (on create).
- `_internal/audit`, `_internal/trash`.

## Consumed by

Every `_workspace` module — none of them function without a workspace id.

## Submodules

- [workspace-config](./config.md)
- [workspace-member](./member.md)
- [workspace-relate](./relate.md)
- [workspace-seeder](./seeder.md)
