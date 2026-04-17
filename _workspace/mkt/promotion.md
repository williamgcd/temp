# mkt-promotion

Time-limited discount or special offer applied to catalog items.

## Purpose

Run sales, coupons, and automatic promotions scoped to items, categories, or segments.

## Responsibilities

- Define a promotion: scope, discount type, validity window, usage caps.
- Validate codes at checkout; apply automatic promotions without a code.
- Track redemptions for reporting.

## Data model

- `mkt_promotion`: `id`, `workspace_id`, `code?`, `name`, `scope_json` (items/categories/segments), `discount_type` (`percent`, `fixed`), `discount_value`, `starts_at?`, `ends_at?`, `max_redemptions?`, `per_client_limit?`, `status` (`draft`, `active`, `paused`, `ended`).
- `mkt_promotion_redemption`: `id`, `promotion_id`, `client_id`, `invoice_id`, `redeemed_at`.

## Public API

- `mkt_promotion.create(...)` / `mkt_promotion.update(id, patch)` / `mkt_promotion.pause(id)` / `mkt_promotion.end(id)`
- `mkt_promotion.quote({ workspace_id, client_id?, items, code? })` — returns discount breakdown.
- `mkt_promotion.apply(invoice_id, promotion_id)`

## Events emitted

- `mkt_promotion.created`, `mkt_promotion.applied`, `mkt_promotion.ended`.

## Depends on

- `_workspace/client`, `_workspace/catalog`, `_workspace/taxonomy`.

## Consumed by

- `_workspace/finance-invoice`.
