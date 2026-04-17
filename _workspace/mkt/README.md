# mkt

Root marketing module grouping outreach and retention tools.

## Purpose

Help workspaces attract, retain, and reward clients.

## Modules

| Module | Definition |
|---|---|
| [mkt-campaign](./campaign.md) | Targeted messaging campaign sent to a segment of clients. |
| [mkt-referral](./referral.md) | Referral program tracking who referred whom and associated rewards. |
| [mkt-loyalty](./loyalty.md) | Points or reward system for repeat clients. |
| [mkt-promotion](./promotion.md) | Time-limited discount or special offer applied to catalog items. |
| [mkt-giftcard](./giftcard.md) | Prepaid value card redeemable against purchases. |

## Shared conventions

- Segments are computed queries over `_workspace/client` and event history.
- Every send, redemption, and reward lands in the event bus for attribution.
- Rewards and discounts compose in a fixed order: promotion → loyalty → giftcard.
