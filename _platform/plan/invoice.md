# plan-invoice

Billing invoice generated for a plan subscription cycle.

## Purpose

Represent the amount owed for a subscription period, broken down by line items, taxes, and discounts. Separate from `_workspace/finance-invoice`, which is client-facing.

## Responsibilities

- Generate an invoice at the start of each billing cycle.
- Apply prorations on plan changes.
- Finalize, mark paid, void, or write off.
- Produce a downloadable PDF and a signed public URL.

## Data model

- `plan_invoice`: `id`, `account_id`, `subscription_id`, `number`, `status` (`draft`, `open`, `paid`, `uncollectible`, `void`), `subtotal_cents`, `tax_cents`, `total_cents`, `currency`, `issued_at?`, `due_at?`, `paid_at?`.
- `plan_invoice_line`: `id`, `invoice_id`, `description`, `quantity`, `unit_cents`, `amount_cents`.

## Public API

- `plan_invoice.draft(subscription_id)`
- `plan_invoice.finalize(id)`
- `plan_invoice.mark_paid(id, payment_id)`
- `plan_invoice.void(id)`

## Events emitted

- `plan_invoice.drafted`, `plan_invoice.finalized`, `plan_invoice.paid`, `plan_invoice.voided`.

## Depends on

- `_platform/plan-subscription`, `_platform/account`.
- `_internal/token` (signed PDF links), `_internal/audit`.

## Consumed by

- `_platform/plan-payment`.
- Account billing UI.
