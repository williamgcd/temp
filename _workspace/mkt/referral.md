# mkt-referral

Referral program tracking who referred whom and associated rewards.

## Purpose

Reward existing clients for bringing new ones; attribute the new client to the referrer for analytics.

## Responsibilities

- Issue unique referral codes per client.
- Validate codes at signup or checkout.
- Grant rewards to both sides on qualifying events (first booking, first paid invoice).
- Expose program configuration per workspace.

## Data model

- `mkt_referral_program`: `workspace_id` (pk), `enabled`, `reward_referrer_json`, `reward_referee_json`, `qualifying_event`.
- `mkt_referral_code`: `id`, `workspace_id`, `code`, `owner_client_id`, `uses_count`, `created_at`.
- `mkt_referral_attribution`: `id`, `referral_code_id`, `referred_client_id`, `qualified_at?`, `reward_referrer_granted_at?`, `reward_referee_granted_at?`.

## Public API

- `mkt_referral.issue_code(client_id)`
- `mkt_referral.apply(code, referred_client_id)`
- `mkt_referral.on_qualifying_event(client_id, event)`

## Events emitted

- `mkt_referral.applied`, `mkt_referral.qualified`, `mkt_referral.reward_granted`.

## Depends on

- `_workspace/client`, `_workspace/mkt-loyalty`, `_workspace/mkt-giftcard`, `_workspace/mkt-promotion` (reward payloads).

## Consumed by

- `_workspace/ai-insight` (program ROI).
