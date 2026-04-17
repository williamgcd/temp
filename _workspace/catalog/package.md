# catalog-package

Bundled set of services or products sold together at a single price.

## Purpose

Offer discounted bundles (e.g. "5 sessions for the price of 4"), gift bundles, or mixed product+service kits.

## Responsibilities

- Compose a package from other catalog items with quantities.
- Compute bundle price (fixed or percent discount).
- Track redemption of the package's components per purchaser.

## Data model

- `catalog_package`: `catalog_item_id` (pk), `items_json` (`[{ item_id, quantity }]`), `pricing_mode` (`fixed`, `percent_off`), `pricing_value`, `expires_after_days?`.

## Public API

- `catalog_package.create(...)` / `catalog_package.update(...)`
- `catalog_package.price(id)` — computed price.
- `catalog_package.components(id)` — expanded items.

## Events emitted

- `catalog_package.created`, `catalog_package.updated`.

## Depends on

- `_workspace/catalog` plus sibling item types.

## Consumed by

- `_workspace/client-package`, `_workspace/finance-invoice`.
