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

- `client_package`: `id`, `workspace_id`, `client_id`, `package_id`, `acquired_via` (`purchase`, `gift`, `manual`), `acquired_at`, `expires_at?`, `status` (`active`, `expired`, `consumed`).
- `client_package_redemption`: `id`, `client_package_id`, `item_id`, `quantity`, `source_booking_id?`, `redeemed_at`.

## Public API

- `client_package.grant({ workspace_id, client_id, package_id, acquired_via })`
- `client_package.redeem({ client_package_id, item_id, quantity, booking_id? })`
- `client_package.remaining(client_package_id)`

## Events emitted

- `client_package.granted`, `client_package.redeemed`, `client_package.expired`, `client_package.consumed`.

## Depends on

- `_workspace/client`, `_workspace/catalog-package`.
- `_workspace/finance-invoice` (purchase flow).

## Consumed by

- `_workspace/schedule-booking` (check package balance).
