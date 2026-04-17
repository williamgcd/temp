# plan

Subscription tier definition with feature flags and limits.

## Purpose

Describe what a given pricing tier unlocks. Plans are global catalog entries referenced by subscriptions; they are not per-account.

## Responsibilities

- Define plan metadata: name, code, price, interval, currency.
- Declare feature flags and numeric limits (workspaces, members, AI calls).
- Version plans so historical subscriptions remain consistent when a plan is updated.

## Data model

- `plan`: `id`, `code`, `name`, `price_cents`, `currency`, `interval` (`month`, `year`), `features_json`, `limits_json`, `status` (`draft`, `active`, `retired`), `version`, `created_at`.

## Public API

- `plan.list({ status })`
- `plan.get(code)` / `plan.get_by_id(id)`
- `plan.create_version(code, changes)` — retires previous version.

## Events emitted

- `plan.created`, `plan.version_created`, `plan.retired`.

## Depends on

- `_internal/audit`.

## Consumed by

- `_platform/plan-subscription`.
- `_platform/account-config`, `_platform/workspace-config` (feature resolution).

## Submodules

- [plan-subscription](./subscription.md)
- [plan-invoice](./invoice.md)
- [plan-payment](./payment.md)
