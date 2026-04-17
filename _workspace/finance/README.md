# finance

Root finance module grouping all monetary records for a workspace.

## Purpose

Represent the flow of money in and out of a workspace: what was billed, what was received, what was spent, and the running totals.

## Responsibilities

- Own the currency and tax context for the workspace (delegated from `workspace-config`).
- Provide consistent money handling (rounding, currency math) across submodules.
- Act as the entry point for finance reporting.

## Data model

This module is a logical parent; durable data lives in the submodules.

## Public API

- `finance.overview(workspace_id, range)` — high-level totals.
- `finance.currency(workspace_id)`

## Events emitted

None directly. Submodules emit.

## Depends on

- `_platform/workspace-config`.

## Consumed by

- Dashboard, `ai-insight`, `finance-tracker`.

## Submodules

- [finance-invoice](./invoice.md)
- [finance-payment](./payment.md)
- [finance-expense](./expense.md)
- [finance-tracker](./tracker.md)
