# _internal

Cross-cutting infrastructure used by the rest of the system. These modules have no business meaning on their own — they are the plumbing every other module leans on.

## Modules

| Module | Definition |
|---|---|
| [audit](./audit.md) | Immutable log of who did what and when across all modules. |
| [cache](./cache.md) | TTL-based key-value cache layer for reducing redundant computation. |
| [token](./token.md) | Short-lived signed tokens used for auth sessions and OTP flows. |
| [track](./track.md) | Event tracking for user and system actions used in analytics. |
| [trash](./trash.md) | Soft-delete bin with auto-purge for recoverable record deletion. |

## Rules

- Internal modules never depend on `_platform` or `_workspace`.
- They expose small, stable APIs; breaking changes cascade everywhere.
- They are multi-tenant-agnostic: callers pass the tenant context, internal modules do not infer it.
