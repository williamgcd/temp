# account-config

Account-level configuration and feature overrides.

## Purpose

Hold tunables that apply to every workspace under the account, plus any overrides that differ from the plan defaults.

## Responsibilities

- Store a sparse set of key/value overrides per account.
- Merge with plan defaults on read; overrides always win.
- Surface which keys are overridable (plan metadata).

## Data model

- `account_config`: `account_id` (pk), `key`, `value_json`, `updated_by`, `updated_at`.

Composite primary key on (`account_id`, `key`).

## Public API

- `account_config.get(account_id, key)` — resolved value (override → plan default).
- `account_config.set(account_id, key, value)`
- `account_config.unset(account_id, key)`
- `account_config.all(account_id)`

## Events emitted

- `account_config.changed`.

## Depends on

- `_platform/account`, `_platform/plan`.
- `_internal/cache`, `_internal/audit`.

## Consumed by

- `_platform/workspace-config` (cascades on read).
- Any module reading feature flags.
