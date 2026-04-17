# document

File or document associated with a client or workspace.

## Purpose

Central file store for anything non-transactional: intake PDFs, client photos, receipts, contracts, exports.

## Responsibilities

- Upload files (direct or signed-URL), storing content in object storage.
- Attach documents to one or more owners (client, booking, invoice, expense).
- Version documents on re-upload; keep old versions unless purged.
- Deliver via short-lived signed URLs.

## Data model

- `document`: `id`, `workspace_id`, `kind`, `name`, `mime`, `size_bytes`, `storage_key`, `owner_type?`, `owner_id?`, `version`, `created_by`, `created_at`.

## Public API

- `document.upload_url({ workspace_id, name, mime, owner? })` — signed upload.
- `document.attach({ document_id, owner_type, owner_id })`
- `document.download_url(id, ttl?)`
- `document.versions(id)`

## Events emitted

- `document.uploaded`, `document.attached`, `document.version_replaced`.

## Depends on

- `_internal/token` (signed URLs), `_internal/audit`, `_internal/trash`.

## Consumed by

- `_workspace/client-journal`, `_workspace/finance-expense`, `_workspace/document-sign`.

## Submodules

- [document-sign](./sign.md)
