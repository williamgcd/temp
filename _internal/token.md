# token

Short-lived signed tokens for auth sessions and OTP flows.

## Purpose

Issue, verify, and revoke opaque or signed tokens used as bearer credentials for sessions, one-time codes, email links, and API keys.

## Responsibilities

- Generate tokens with a defined `kind`, `subject`, `scope`, and `ttl`.
- Verify signature and expiry; return claims on success.
- Single-use enforcement for OTP and magic-link kinds.
- Revocation registry for session tokens.

## Data model

- `token`: `id`, `kind` (`session`, `otp`, `magic_link`, `api_key`), `subject_type`, `subject_id`, `scope`, `issued_at`, `expires_at`, `used_at?`, `revoked_at?`.

## Public API

- `token.issue({ kind, subject, scope, ttl })`
- `token.verify(raw)` → claims or error.
- `token.consume(raw)` — for single-use kinds.
- `token.revoke(id)`.

## Events emitted

- `token.issued`, `token.consumed`, `token.revoked`.

## Depends on

Nothing inside the system.

## Consumed by

- `_platform/auth` for sessions and OTP.
- `_platform/user-oauth` for state tokens.
- Any module issuing signed links (invoices, forms, signatures).
