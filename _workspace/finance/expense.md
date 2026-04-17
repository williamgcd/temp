# finance-expense

Outgoing cost tracked against the workspace's finances.

## Purpose

Give the operator a lightweight expense log so profit can be computed, not just revenue.

## Responsibilities

- Record an expense with category, vendor, amount, date, and attachment (receipt).
- Support recurring expenses (rent, utilities).
- Feed `finance-tracker` for P&L views.

## Data model

- `finance_expense`: `id`, `workspace_id`, `category`, `vendor?`, `description?`, `amount_cents`, `currency`, `incurred_on`, `recurrence?`, `receipt_document_id?`, `created_by`, `created_at`.

## Public API

- `finance_expense.record(...)` / `finance_expense.update(id, patch)` / `finance_expense.delete(id)`
- `finance_expense.list({ workspace_id, category?, range })`

## Events emitted

- `finance_expense.recorded`, `finance_expense.updated`, `finance_expense.deleted`.

## Depends on

- `_workspace/document` (receipts).
- `_internal/audit`, `_internal/trash`.

## Consumed by

- `_workspace/finance-tracker`, `_workspace/ai-insight`.
