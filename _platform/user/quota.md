# user-quota

Usage limits enforced per user across the platform.

## Purpose

Prevent abuse and bound cost by capping user-scoped actions (logins, OTP requests, AI calls, exports).

## Responsibilities

- Define quota keys with a window and limit.
- Track consumption per user per window.
- Reject or throttle when the limit is reached; expose remaining count.

## Data model

- `user_quota_def`: `key`, `window` (`minute`, `hour`, `day`, `month`), `limit`, `action_on_exceed`.
- `user_quota_usage`: `user_id`, `key`, `window_start`, `count`.

## Public API

- `user_quota.check(user_id, key)` → `{ allowed, remaining, reset_at }`.
- `user_quota.consume(user_id, key, n=1)` → `{ allowed, remaining, reset_at }`.
- `user_quota.reset(user_id, key)` — admin.

## Events emitted

- `user_quota.exceeded`.

## Depends on

- `_internal/cache`.

## Consumed by

- `_platform/auth` (OTP / login rate limits).
- `_platform/llm-action` (AI usage caps).
