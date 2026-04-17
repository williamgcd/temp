# auth

Authentication flows: OTP verification, session creation, token issuance.

## Purpose

Turn a claimed identity into an authenticated session. Owns the login, verification, and logout flows end-to-end.

## Responsibilities

- Start OTP flow: find/create user, issue OTP token, dispatch via messaging.
- Verify OTP: validate token, mint session token, return session payload.
- OAuth callback: exchange code, upsert `user-oauth`, mint session.
- Session refresh, revoke, logout.
- Rate-limit attempts per user and IP.

## Data model

No durable rows of its own — coordinates `user`, `user-oauth`, and `token`.

## Public API

- `auth.start_otp({ email | phone })`
- `auth.verify_otp({ subject, code })` → session token.
- `auth.oauth_callback({ provider, code, state })` → session token.
- `auth.refresh(session_token)` / `auth.logout(session_token)`

## Events emitted

- `auth.otp_sent`, `auth.session_started`, `auth.session_refreshed`, `auth.session_revoked`, `auth.login_failed`.

## Depends on

- `_platform/user`, `_platform/user-oauth`, `_platform/user-quota`.
- `_internal/token`, `_internal/audit`, `_internal/cache`.

## Consumed by

- The HTTP edge (login/logout endpoints).
- Any backend that needs to rotate or revoke a session.
