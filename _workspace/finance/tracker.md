# finance-tracker

Aggregated financial summary used for reporting and monitoring.

## Purpose

Precomputed, query-friendly views over invoices, payments, and expenses so dashboards and insights don't thrash the source tables.

## Responsibilities

- Listen to finance events and update rolled-up counters (daily/weekly/monthly).
- Serve dashboard queries: revenue, outstanding, refunds, expenses, net.
- Invalidate and recompute on void/refund events.

## Data model

- `finance_tracker_bucket`: `workspace_id`, `granularity` (`day`, `week`, `month`), `period_start`, `revenue_cents`, `paid_cents`, `refund_cents`, `expense_cents`, `outstanding_cents`, `booking_count`, `updated_at`.

## Public API

- `finance_tracker.series({ workspace_id, granularity, range, metric })`
- `finance_tracker.totals({ workspace_id, range })`
- `finance_tracker.rebuild(workspace_id, range)` — admin.

## Events emitted

- `finance_tracker.rebuilt`.

## Depends on

- `_workspace/finance-invoice`, `_workspace/finance-payment`, `_workspace/finance-expense` (via events).
- `_internal/cache`.

## Consumed by

- Dashboard UI, `_workspace/ai-insight`.
