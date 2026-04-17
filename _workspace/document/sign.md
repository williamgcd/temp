# document-sign

Signature request and status tracking for a document.

## Purpose

Let a workspace send a document for electronic signature — consent forms, contracts, waivers — and track when each signer completes.

## Responsibilities

- Define one or more signers per request with signing order.
- Deliver a signing link per signer via a signed token.
- Record signature events with IP, user agent, and timestamp.
- Generate a final signed PDF with audit trail appended.

## Data model

- `document_sign_request`: `id`, `workspace_id`, `document_id`, `status` (`pending`, `signed`, `declined`, `expired`, `voided`), `expires_at?`, `created_by`, `created_at`.
- `document_sign_signer`: `id`, `request_id`, `order`, `client_id?`, `email`, `status`, `signed_at?`, `ip?`, `ua?`, `signature_image_id?`.

## Public API

- `document_sign.request({ document_id, signers[] })`
- `document_sign.sign(signer_token, signature)` — called from signer UI.
- `document_sign.void(request_id, reason)`
- `document_sign.final_pdf(request_id)`

## Events emitted

- `document_sign.requested`, `document_sign.signed`, `document_sign.completed`, `document_sign.declined`, `document_sign.expired`.

## Depends on

- `_workspace/document`, `_workspace/client`.
- `_internal/token` (per-signer links), `_internal/audit`.

## Consumed by

- `_workspace/client-journal`, `_workspace/form-response` (consent workflows).
