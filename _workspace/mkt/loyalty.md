# mkt-loyalty

Points or reward system for repeat clients.

## Purpose

Reward frequency and spend with points that accrue and redeem against catalog items.

## Responsibilities

- Define earn rules (per currency unit, per booking completed, per campaign click).
- Define redemption catalog (points → discount / item).
- Maintain per-client point balance with audit history.
- Expire unused points per policy.

## Data model

- `mkt_loyalty_program`: `workspace_id` (pk), `name`, `earn_rules_json`, `redemption_rules_json`, `expiry_days?`.
- `mkt_loyalty_balance`: `workspace_id`, `client_id`, `points`, `updated_at`.
- `mkt_loyalty_ledger`: `id`, `client_id`, `delta`, `reason`, `source_ref?`, `occurred_at`.

## Public API

- `mkt_loyalty.earn(client_id, delta, reason, source_ref?)`
- `mkt_loyalty.redeem(client_id, delta, reason, source_ref?)`
- `mkt_loyalty.balance(client_id)`

## Events emitted

- `mkt_loyalty.earned`, `mkt_loyalty.redeemed`, `mkt_loyalty.expired`.

## Depends on

- `_workspace/client`, `_workspace/finance-invoice`, `_workspace/schedule-booking` (events).

## Consumed by

- `_workspace/finance-invoice` (redemption as discount).
