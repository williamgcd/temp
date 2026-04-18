# client

Core client profile (individual or organization) tied to a workspace.

## Purpose

Who the workspace sells to. Distinct from `_platform/user`: a client may never sign in; it is a workspace-scoped contact.

## Responsibilities

- Identity and contact (name, email, phone, address, birthday, pronouns, tags).
- Allow name-only creation; fill contact fields progressively via Silvia.
- Dedupe within a workspace using verified email or phone, plus assisted merge for fuzzy duplicates.
- Organization clients with child individuals.
- Link to a platform `user` when the client logs into the client portal.
- Aggregate read for the client detail screen.

## Data model

- `client`: `id`, `workspace_id`, `kind` (`individual`, `organization`), `name`, `email?`, `phone?`, `birthday?`, `address_json?`, `notes?`, `tags?`, `linked_user_id?`, `parent_client_id?`, `merged_into?`, `status` (`active`, `blocked`, `archived`, `merged`), `created_at`.

Only `name` is required on create. `email`, `phone`, and the rest are nullable and get filled in over time. `merged_into` points at the master client when this row was consolidated via `merge`; the row is preserved for audit and reversal.

### Derived fields (returned by `list` / `get` / `get_full`)

- `last_visit_at` — most recent `schedule_booking.starts_at` with status in `completed | confirmed`. Cached; invalidated on booking events.
- `next_booking_at` — soonest future booking.

## Public API

### CRUD

- `client.create({ workspace_id, name, ...optional })` — name-only accepted.
- `client.update(id, patch)`
- `client.archive(id)` — soft; `status = archived`. Restore via `update(id, { status: 'active' })`.
- `client.block(id, reason?)` — `status = blocked`. Hidden from public booking, Silvia rejects requests.
- `client.soft_delete(id)` — LGPD path: routes through `_internal/trash` for retention then purge. See use-case in `09-settings/data-privacy.md` (TBD).

### Lookup

- `client.get(id)`
- `client.list({ workspace_id, search?, filter?, sort?, page?, page_size? })` — main list endpoint.
  - **Filters**: `active`, `archived`, `blocked`, `dormant_days_gte`, `has_active_package`, `has_open_invoice`, `birthday_month`, `birthday_week`, `tag` (any of), `last_service_id`.
  - **Sort**: `last_visit_desc` (default), `name_asc`, `frequency_desc`, `avg_ticket_desc`, `created_desc`.
- `client.get_full(workspace_id, id)` — aggregated payload for the client detail screen: `{ client, stats, next_booking, bookings_recent, packages_active, invoices_open, journal_recent, forms_recent, silvia_summary }`. Cache-backed; invalidated by relevant downstream events.
- `client.find_or_create({ workspace_id, email | phone })` — dedupe by verified contact.
- `client.find_or_create_by_name({ workspace_id, name })` — fuzzy name match (case-insensitive, accent-insensitive); creates when no confident match. Used by `schedule_booking.create_minimal`.

### Import

- `client.import_from_contacts(workspace_id, entries[])` — batch upsert from OS contact picker; uses `find_or_create_by_name` per entry; returns `{ created, matched, skipped }`.
- `client.import_from_csv(workspace_id, rows[], mapping)` — CSV import with column mapping.

### Dedupe / merge

- `client.find_duplicates(workspace_id)` — returns candidate pairs with similarity score (phone, email, fuzzy name, pattern overlap).
- `client.merge({ master_id, from_ids[], field_choices })` — consolidate. Reassigns referencing rows in `schedule-booking`, `finance-invoice`, `client-journal`, `client-package`, `form-response`. Returns `merge_token`.
- `client.unmerge(merge_token)` — reverse a merge within 7 days.

### Linking

- `client.link_user(id, user_id)` — connect to a `_platform/user` for the client portal.

## Events emitted

- `client.created`, `client.updated`, `client.blocked`, `client.archived`, `client.unarchived`, `client.user_linked`.
- `client.merged` (carries `master_id`, `from_ids`, `merge_token`), `client.unmerged`.
- `client.imported_batch`.

## Depends on

- `_platform/workspace`, `_platform/user` (optional link).
- `_internal/audit`, `_internal/trash`, `_internal/cache`.
- `_workspace/schedule-booking`, `_workspace/finance-invoice`, `_workspace/client-package`, `_workspace/client-journal`, `_workspace/form-response` (read-only, for `get_full`).

## Consumed by

- `_workspace/schedule-booking`, `_workspace/finance-invoice`, `_workspace/mkt-*`, `_workspace/form-response`, `_workspace/schedule-public-link`.

## Submodules

- [client-package](./package.md)
- [client-journal](./journal.md)
