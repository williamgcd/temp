# finance-payment

Payment record applied against an invoice or balance.

## Purpose

Record the inflow side: cash paid at the counter, card charges via a processor, bank transfers, gift-card redemptions.

## Responsibilities

- Persist payment attempts and settlements with provider refs.
- Apply to one or many invoices (partial or split payments).
- Handle refunds and chargebacks.
- Reconcile across provider webhooks.

## Data model

- `finance_payment`: `id`, `workspace_id`, `client_id`, `method` (`cash`, `card`, `transfer`, `giftcard`, `other`), `provider?`, `provider_ref?`, `amount_cents`, `currency`, `status` (`pending`, `succeeded`, `failed`, `refunded`), `received_at`.
- `finance_payment_application`: `payment_id`, `invoice_id`, `amount_cents`.

## Public API

- `finance_payment.record(...)` — manual entry.
- `finance_payment.charge(invoice_id, method, ...)` — processor flow.
- `finance_payment.apply(payment_id, invoice_id, amount)`
- `finance_payment.refund(id, amount?)`

## Events emitted

- `finance_payment.recorded`, `finance_payment.applied`, `finance_payment.refunded`.

## Depends on

- `_workspace/finance-invoice`, `_workspace/mkt-giftcard`.
- External payment provider.

## Consumed by

- `_workspace/finance-invoice` (status transitions), `_workspace/finance-tracker`.
