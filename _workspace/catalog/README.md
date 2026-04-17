# catalog

Root catalog grouping all sellable offerings for a workspace.

## Purpose

The single source of truth for what a workspace sells — products, services, courses, and packages.

## Responsibilities

- Organize items by taxonomy and tags.
- Expose a unified listing/search API across item types.
- Coordinate pricing, visibility, and availability flags.

## Data model

- `catalog_item`: `id`, `workspace_id`, `type` (`product`, `service`, `course`, `package`), `name`, `sku?`, `price_cents`, `currency`, `visibility` (`public`, `private`), `status` (`draft`, `active`, `archived`), `taxonomy_ids[]`, `tags[]`, `created_at`.

Each subtype extends with its own child table.

## Public API

- `catalog.list({ workspace_id, type?, visibility?, search? })`
- `catalog.get(id)`
- `catalog.archive(id)` / `catalog.publish(id)`

## Events emitted

- `catalog.item_created`, `catalog.item_updated`, `catalog.item_archived`.

## Depends on

- `_workspace/taxonomy`.
- `_internal/audit`, `_internal/trash`.

## Consumed by

- `_workspace/schedule-booking`, `_workspace/finance-invoice`, `_workspace/mkt-promotion`.

## Submodules

- [catalog-product](./product.md)
- [catalog-service](./service.md)
- [catalog-course](./course.md)
- [catalog-package](./package.md)
