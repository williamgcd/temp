# client

Core client profile (individual or organization) tied to a workspace.

## Purpose

Who the workspace sells to. Distinct from `_platform/user`: a client may never sign in; it is a workspace-scoped contact.

## Responsibilities

- Identity and contact (name, email, phone, address, birthday, pronouns).
- Dedupe within a workspace using verified email or phone.
- Organization clients with child individuals.
- Link to a platform `user` when the client logs into the client portal.

## Data model

- `client`: `id`, `workspace_id`, `kind` (`individual`, `organization`), `name`, `email?`, `phone?`, `birthday?`, `address_json?`, `notes?`, `linked_user_id?`, `parent_client_id?`, `status` (`active`, `blocked`, `archived`), `created_at`.

## Public API

- `client.create({ workspace_id, ... })` / `client.update(id, patch)`
- `client.find_or_create({ workspace_id, email | phone })`
- `client.block(id, reason)` / `client.archive(id)`
- `client.link_user(id, user_id)`

## Events emitted

- `client.created`, `client.updated`, `client.blocked`, `client.archived`, `client.user_linked`.

## Depends on

- `_platform/workspace`, `_platform/user` (optional link).
- `_internal/audit`, `_internal/trash`.

## Consumed by

- `_workspace/schedule-booking`, `_workspace/finance-invoice`, `_workspace/mkt-*`, `_workspace/form-response`.

## Submodules

- [client-package](./package.md)
- [client-journal](./journal.md)
