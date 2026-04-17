# plan-payment

Payment record applied against a plan invoice.

## Purpose

Track attempts and successful charges made against `plan-invoice`, including retries and dunning.

## Responsibilities

- Persist payment attempts with provider refs.
- Retry failed payments on a schedule (dunning).
- Apply successful payments to invoices.
- Handle refunds.

## Data model

- `plan_payment`: `id`, `account_id`, `invoice_id`, `provider`, `provider_ref`, `amount_cents`, `currency`, `status` (`pending`, `succeeded`, `failed`, `refunded`), `failure_code?`, `attempted_at`, `settled_at?`.

## Public API

- `plan_payment.charge(invoice_id)` — attempt against the default payment method.
- `plan_payment.refund(id, amount_cents?)`
- `plan_payment.record_webhook(provider, payload)` — idempotent provider callback handler.

## Events emitted

- `plan_payment.attempted`, `plan_payment.succeeded`, `plan_payment.failed`, `plan_payment.refunded`.

## Depends on

- `_platform/plan-invoice`, `_platform/account`.
- External payment provider SDK.

## Consumed by

- `_platform/plan-subscription` (past_due transitions).
- Account billing UI.
