# plan-subscription

A workspace's (or account's) active subscription to a plan.

## Purpose

Bind a billable entity to a plan version with a lifecycle, trial state, and renewal schedule.

## Responsibilities

- Create, upgrade, downgrade, cancel subscriptions.
- Track trial windows and convert to paid.
- Emit renewal events that drive `plan-invoice` generation.
- Expose the active feature flag/limit resolver.

## Data model

- `plan_subscription`: `id`, `account_id`, `workspace_id?`, `plan_id`, `status` (`trialing`, `active`, `past_due`, `canceled`, `expired`), `trial_ends_at?`, `current_period_start`, `current_period_end`, `cancel_at?`, `canceled_at?`.

## Public API

- `plan_subscription.start({ account_id, workspace_id?, plan_code, trial_days? })`
- `plan_subscription.change_plan(id, plan_code)`
- `plan_subscription.cancel(id, at_period_end=true)`
- `plan_subscription.resolve_features(workspace_id)` → merged flags and limits.

## Events emitted

- `plan_subscription.started`, `plan_subscription.changed`, `plan_subscription.renewed`, `plan_subscription.past_due`, `plan_subscription.canceled`, `plan_subscription.expired`.

## Depends on

- `_platform/plan`, `_platform/account`, `_platform/workspace`.

## Consumed by

- `_platform/plan-invoice`.
- Feature-gated code everywhere.
