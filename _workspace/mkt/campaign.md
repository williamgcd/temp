# mkt-campaign

Targeted messaging campaign sent to a segment of clients.

## Purpose

One-off or scheduled outbound messaging: reactivation, birthday promo, announcement.

## Responsibilities

- Define a segment (filter over clients + history).
- Compose a message (template, optionally AI-drafted).
- Schedule a send window and channel (email, SMS).
- Track delivery, opens, clicks, conversions.

## Data model

- `mkt_campaign`: `id`, `workspace_id`, `name`, `segment_json`, `channel`, `template_id`, `scheduled_for?`, `status` (`draft`, `scheduled`, `sending`, `sent`, `cancelled`), `created_by`, `created_at`.
- `mkt_campaign_send`: `id`, `campaign_id`, `client_id`, `status`, `sent_at?`, `opened_at?`, `clicked_at?`, `converted_booking_id?`.

## Public API

- `mkt_campaign.draft(...)` / `mkt_campaign.schedule(id, at)` / `mkt_campaign.cancel(id)`
- `mkt_campaign.preview(id)` — returns sample recipients.
- `mkt_campaign.stats(id)` — delivery and conversion metrics.

## Events emitted

- `mkt_campaign.scheduled`, `mkt_campaign.sent`, `mkt_campaign.delivered`, `mkt_campaign.opened`, `mkt_campaign.clicked`, `mkt_campaign.converted`.

## Depends on

- `_workspace/client`, `_workspace/ai-message` (AI drafts), external messaging provider.

## Consumed by

- `_workspace/ai-insight` (campaign effectiveness), `_workspace/finance-tracker` (attribution).
