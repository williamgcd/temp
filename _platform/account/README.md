# account

Top-level billing entity that owns one or more workspaces.

## Purpose

Represent the legal/commercial counterparty for the platform. All billing, plans, and cross-workspace ownership hang off of an account.

## Responsibilities

- Create and lifecycle-manage accounts (active, suspended, closed).
- Link to owning and admin users.
- Parent all workspaces created under the account.
- Hold legal/tax details used on invoices.

## Data model

- `account`: `id`, `name`, `owner_user_id`, `legal_name`, `tax_id?`, `country`, `status` (`active`, `suspended`, `closed`), `created_at`.

## Public API

- `account.create({ owner_user_id, name })`
- `account.update(id, patch)`
- `account.suspend(id, reason)` / `account.reactivate(id)`
- `account.close(id)`

## Events emitted

- `account.created`, `account.updated`, `account.suspended`, `account.reactivated`, `account.closed`.

## Depends on

- `_platform/user` (owner).
- `_internal/audit`, `_internal/trash`.

## Consumed by

- `_platform/workspace` (parent).
- `_platform/plan-subscription`, `_platform/plan-invoice`.

## Submodules

- [account-config](./config.md)
