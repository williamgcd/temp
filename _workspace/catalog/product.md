# catalog-product

Physical or digital product listed in the catalog.

## Purpose

Represent items with inventory (physical) or a fulfillment artifact (digital): retail items, take-home goods, downloadable files.

## Responsibilities

- Track stock levels and reorder thresholds for physical products.
- Handle digital delivery (signed links) for digital products.
- Emit low-stock and out-of-stock events.

## Data model

- `catalog_product`: `catalog_item_id` (pk), `kind` (`physical`, `digital`), `sku`, `barcode?`, `stock?`, `low_stock_threshold?`, `digital_asset_url?`.

## Public API

- `catalog_product.create(...)` / `catalog_product.update(...)`
- `catalog_product.adjust_stock(id, delta, reason)`
- `catalog_product.deliver(id, recipient)` — digital only.

## Events emitted

- `catalog_product.stock_changed`, `catalog_product.low_stock`, `catalog_product.out_of_stock`, `catalog_product.delivered`.

## Depends on

- `_workspace/catalog` (parent).
- `_internal/token` (signed download links).

## Consumed by

- `_workspace/finance-invoice` (line items).
