# user-oauth

OAuth provider tokens linked to a user for third-party login.

## Purpose

Store per-provider linkage so a user can sign in with Google/Apple/etc., and so backend jobs can call provider APIs on the user's behalf.

## Responsibilities

- Upsert provider identity on OAuth callback.
- Store refresh tokens securely; rotate on expiry.
- Revoke on logout or on provider-side revocation signals.

## Data model

- `user_oauth`: `id`, `user_id`, `provider`, `provider_user_id`, `access_token_enc`, `refresh_token_enc?`, `scopes`, `expires_at?`, `linked_at`, `revoked_at?`.

Unique on (`provider`, `provider_user_id`).

## Public API

- `user_oauth.link({ user_id, provider, tokens, scopes })`
- `user_oauth.token(user_id, provider)` — returns a fresh access token, refreshing if needed.
- `user_oauth.revoke(id)`

## Events emitted

- `user_oauth.linked`, `user_oauth.refreshed`, `user_oauth.revoked`.

## Depends on

- `_platform/user`, `_internal/token`, `_internal/audit`.

## Consumed by

- `_platform/auth` (social login).
- Any integration that calls a provider API on the user's behalf.
