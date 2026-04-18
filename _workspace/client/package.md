# client-package

Package purchased or assigned to a specific client.

## Purpose

Track the redeemable balance of a `catalog-package` owned by a client — how many sessions left, which items still available, when it expires.

## Responsibilities

- Create an instance on purchase or manual grant.
- Decrement on redemption (booking, product pickup).
- Expire on schedule or on package-level rules.
- Expose remaining quantity per component.

## Data model

- `client_package`: `id`, `workspace_id`, `client_id`, `purchased_by_client_id?`, `package_id`, `acquired_via` (`purchase`, `gift`, `manual`), `acquired_at`, `expires_at?`, `status` (`active`, `expired`, `consumed`, `voided`), `notes?`.
- `client_package_redemption`: `id`, `client_package_id`, `item_id`, `quantity`, `source_booking_id?`, `redeemed_at`.

`purchased_by_client_id` differs from `client_id` only when the buyer is not the user (gift case). The user (consumer) stays in `client_id`.

## Public API

- `client_package.grant({ workspace_id, client_id, package_id, acquired_via, purchased_by_client_id?, expires_at? })`
- `client_package.redeem({ client_package_id, item_id, quantity, booking_id? })`
- `client_package.void(id, reason?)` — for refunds; coordinates with `finance-payment.refund`.
- `client_package.remaining(client_package_id)` — per-component counts.
- `client_package.list_for_client({ workspace_id, client_id, status? })`
- `client_package.list_expiring({ workspace_id, within_days })` — used by warning job.
- `client_package.expire_due()` — periodic job that flips ripe `active` packages to `expired` based on `expires_at`.

## Events emitted

- `client_package.granted`, `client_package.redeemed`, `client_package.expiring_soon` (15d before), `client_package.expired`, `client_package.consumed`, `client_package.voided`.

## Depends on

- `_workspace/client`, `_workspace/catalog-package`.
- `_workspace/finance-invoice` (purchase flow), `_workspace/finance-payment` (refund flow).

## Consumed by

- `_workspace/schedule-booking` (check package balance, link redemption via `redeemed_package_id`).
- `_workspace/ai-action` (rules `auto_redeem_suggestion`, `package_expiration_warning`, `package_upsell`).
