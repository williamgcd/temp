# System Docs

Documentation for the modular SaaS platform. The system is organized into three layers; every module lives in exactly one layer and communicates with others through a well-defined public API and event bus.

## Layers

| Layer | Purpose |
|---|---|
| [`_internal`](./_internal) | Cross-cutting infrastructure used by every other module (logs, audit, tokens, cache, trash, tracking). |
| [`_platform`](./_platform) | Multi-tenant fabric: identities, auth, workspaces, plans, billing, LLM runtime. |
| [`_workspace`](./_workspace) | Business domain a workspace operates: clients, schedule, catalog, finance, marketing, AI, forms, documents. |

## Design references

- [Product](./docs/product.md) — audience, positioning, principles, pricing, onboarding, Silvia (the AI assistant).
- [Personas](./docs/personas.md) — Sabriza, Júlia, Bianca. Who we're building for (solo/self BR beauty pros, v1).
- [Use cases](./docs/use-cases/README.md) — concrete flows that drive module decisions, grouped by area.

## Module doc template

Every module doc includes:

- **Purpose** — one sentence.
- **Responsibilities** — what it owns.
- **Data model** — primary entities and key fields.
- **Public API** — functions / endpoints other modules call.
- **Events emitted** — messages published to the event bus.
- **Depends on** — modules it calls directly.
- **Consumed by** — modules that depend on it.

## Conventions

- **Module naming**: `kebab-case`, grouped with a shared prefix when related (`catalog-product`, `schedule-booking`).
- **Cross-module calls**: always through the module's public API — never by reaching into another module's tables.
- **Events**: past-tense (`booking.created`, `invoice.paid`); fire-and-forget, handled by workers.
- **Tenancy**: every business-domain row carries `workspace_id`; platform rows carry `account_id`; internal rows are global.
- **Soft delete**: handled by `_internal/trash`; modules do not implement their own.
- **Audit**: handled by `_internal/audit`; modules emit events, audit listens.
