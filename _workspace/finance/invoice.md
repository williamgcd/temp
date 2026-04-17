# finance-invoice

Itemized bill issued to a client for services or products.

## Purpose

The client-facing invoice. Distinct from `_platform/plan-invoice` (which bills the workspace for the platform subscription).

## Responsibilities

- Generate invoices from bookings or ad-hoc sales.
- Apply discounts (`mkt-promotion`), package redemptions, and gift cards.
- Compute taxes per workspace tax settings.
- Finalize, send, mark paid, and void.

## Data model

- `finance_invoice`: `id`, `workspace_id`, `client_id`, `number`, `status` (`draft`, `open`, `paid`, `partial`, `void`), `subtotal_cents`, `discount_cents`, `tax_cents`, `total_cents`, `currency`, `issued_at?`, `due_at?`, `paid_at?`.
- `finance_invoice_line`: `id`, `invoice_id`, `item_id?`, `description`, `quantity`, `unit_cents`, `amount_cents`, `booking_id?`.

## Public API

- `finance_invoice.draft({ workspace_id, client_id, lines })`
- `finance_invoice.finalize(id)` — issues number, marks `open`.
- `finance_invoice.send(id)` — emails/signs a link.
- `finance_invoice.void(id)`

## Events emitted

- `finance_invoice.drafted`, `finance_invoice.finalized`, `finance_invoice.sent`, `finance_invoice.paid`, `finance_invoice.voided`.

## Depends on

- `_workspace/client`, `_workspace/catalog`, `_workspace/client-package`, `_workspace/mkt-promotion`, `_workspace/mkt-giftcard`.
- `_internal/token` (signed link), `_internal/audit`.

## Consumed by

- `_workspace/finance-payment`, `_workspace/finance-tracker`.
