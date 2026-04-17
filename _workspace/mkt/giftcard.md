# mkt-giftcard

Prepaid value card redeemable against purchases.

## Purpose

Sell stored-value cards clients can gift or use themselves; redeem against any invoice up to their balance.

## Responsibilities

- Issue cards with an initial balance and unique code.
- Track balance ledger per card.
- Redeem against invoices; refund balance on purchase refund.
- Expire per policy.

## Data model

- `mkt_giftcard`: `id`, `workspace_id`, `code`, `issued_to_client_id?`, `purchased_by_client_id?`, `initial_amount_cents`, `balance_cents`, `currency`, `status` (`active`, `depleted`, `expired`, `void`), `expires_at?`, `issued_at`.
- `mkt_giftcard_ledger`: `id`, `giftcard_id`, `delta_cents`, `reason`, `source_ref?`, `occurred_at`.

## Public API

- `mkt_giftcard.issue({ workspace_id, amount, issued_to?, purchased_by? })`
- `mkt_giftcard.redeem(code, amount, invoice_id)`
- `mkt_giftcard.balance(code)`

## Events emitted

- `mkt_giftcard.issued`, `mkt_giftcard.redeemed`, `mkt_giftcard.depleted`, `mkt_giftcard.expired`.

## Depends on

- `_workspace/client`, `_workspace/finance-invoice`, `_workspace/finance-payment`.

## Consumed by

- `_workspace/finance-payment` (redemption as payment method).
