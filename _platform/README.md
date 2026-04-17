# _platform

Multi-tenant fabric of the system. Platform modules define who can use the product, under which account, at which pricing, with what identity, and expose the LLM runtime that business modules build on.

## Modules

### Identity & tenancy

| Module | Definition |
|---|---|
| [account](./account/README.md) | Top-level billing entity that owns one or more workspaces. |
| [account-config](./account/config.md) | Account-level configuration and feature overrides. |
| [workspace](./workspace/README.md) | Isolated tenant environment with its own data, members, and settings. |
| [workspace-config](./workspace/config.md) | Workspace-level preferences (currency, timezone, language, timeslot). |
| [workspace-member](./workspace/member.md) | A user's role and membership status within a workspace. |
| [workspace-relate](./workspace/relate.md) | Relationships or links between workspaces (e.g. franchises, branches). |
| [workspace-seeder](./workspace/seeder.md) | Seeds a new workspace with default data on creation. |
| [user](./user/README.md) | Platform-wide user identity with OTP auth, contact info, and access status. |
| [user-oauth](./user/oauth.md) | OAuth provider tokens linked to a user for third-party login. |
| [user-quota](./user/quota.md) | Usage limits enforced per user across the platform. |
| [auth](./auth.md) | Authentication flows: OTP verification, session creation, and token issuance. |

### Billing

| Module | Definition |
|---|---|
| [plan](./plan/README.md) | Subscription tier definition with feature flags and limits. |
| [plan-subscription](./plan/subscription.md) | A workspace's active subscription to a plan. |
| [plan-invoice](./plan/invoice.md) | Billing invoice generated for a plan subscription cycle. |
| [plan-payment](./plan/payment.md) | Payment record applied against a plan invoice. |

### LLM runtime

| Module | Definition |
|---|---|
| [llm-vector](./llm/vector.md) | Vector embeddings stored for semantic search over workspace data. |
| [llm-worker](./llm/worker.md) | Background job runner that executes LLM inference tasks. |
| [llm-action](./llm/action.md) | Structured LLM-powered action dispatched and tracked by the platform. |

## Rules

- Platform modules may depend on `_internal` but never on `_workspace`.
- Every row belongs to an `account_id` (for account-scoped) or is global (users, plans).
- Feature limits are enforced at this layer; business modules read flags via `plan-subscription`.
