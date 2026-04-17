# user

Platform-wide user identity with OTP auth, contact info, and access status.

## Purpose

One person = one user, regardless of how many workspaces or accounts they join.

## Responsibilities

- Store identity and contact fields (name, email, phone, avatar).
- Track verification state of email and phone.
- Lifecycle: active, suspended, deleted.
- Dedupe by verified email/phone at login time.

## Data model

- `user`: `id`, `email?`, `email_verified_at?`, `phone?`, `phone_verified_at?`, `full_name`, `avatar_url?`, `status`, `created_at`.

At least one of `email` or `phone` must be present and verified for login.

## Public API

- `user.find_or_create({ email?, phone? })`
- `user.update(id, patch)`
- `user.verify_email(id)` / `user.verify_phone(id)`
- `user.suspend(id)` / `user.delete(id)`

## Events emitted

- `user.created`, `user.updated`, `user.email_verified`, `user.phone_verified`, `user.suspended`, `user.deleted`.

## Depends on

- `_internal/audit`, `_internal/trash`.

## Consumed by

- `_platform/auth`, `_platform/workspace-member`, `_platform/account` (owner).
- `_internal/track` (user_id context).

## Submodules

- [user-oauth](./oauth.md)
- [user-quota](./quota.md)
