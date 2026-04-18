# _workspace

The business domain. These are the modules end users interact with to run their business — clients, schedules, catalog, money, marketing, forms, documents, and the AI layer that operates on top of them.

## Modules

### AI (workspace-specific)

| Module | Definition |
|---|---|
| [ai-persona](./ai/persona.md) | Configurable AI assistant persona with name, tone, and behavior for a workspace. |
| [ai-setting](./ai/setting.md) | Workspace-level AI feature configuration and toggles. |
| [ai-example](./ai/example.md) | Few-shot examples used to guide AI outputs. |
| [ai-insight](./ai/insight.md) | AI-generated analytical summaries and observations from workspace data. |
| [ai-message](./ai/message.md) | AI-composed messages sent to or on behalf of clients. |
| [ai-action](./ai/action.md) | Automated AI-triggered actions executed in response to events. |

### Catalog

| Module | Definition |
|---|---|
| [catalog](./catalog/README.md) | Root catalog grouping all sellable offerings for a workspace. |
| [catalog-product](./catalog/product.md) | Physical or digital product listed in the catalog. |
| [catalog-service](./catalog/service.md) | Service offered by a professional, listed in the catalog. |
| [catalog-course](./catalog/course.md) | Multi-session or structured course offering in the catalog. |
| [catalog-package](./catalog/package.md) | Bundled set of services or products sold together. |

### Clients

| Module | Definition |
|---|---|
| [client](./client/README.md) | Core client profile (individual or organization) tied to a workspace. |
| [client-package](./client/package.md) | Package purchased or assigned to a specific client. |
| [client-journal](./client/journal.md) | Timestamped notes and observations recorded on a client. |

### Schedule

| Module | Definition |
|---|---|
| [schedule](./schedule/README.md) | Core availability and calendar configuration for a workspace or professional. |
| [schedule-booking](./schedule/booking.md) | A confirmed appointment between a client and a professional. |
| [schedule-policy](./schedule/policy.md) | Bookability rules (overlap, windows, fees, buffers, slot granularity, default duration). |
| [schedule-waitlist](./schedule/waitlist.md) | Queue of clients waiting for an opening in a full schedule slot. |
| [schedule-blocker](./schedule/blocker.md) | Time block that marks a period as unavailable for new bookings. |
| [schedule-holiday](./schedule/holiday.md) | Recurring or one-off closure that prevents bookings on specific days. |

### Finance

| Module | Definition |
|---|---|
| [finance](./finance/README.md) | Root finance module grouping all monetary records for a workspace. |
| [finance-invoice](./finance/invoice.md) | Itemized bill issued to a client for services or products. |
| [finance-payment](./finance/payment.md) | Payment record applied against an invoice or balance. |
| [finance-expense](./finance/expense.md) | Outgoing cost tracked against the workspace's finances. |
| [finance-tracker](./finance/tracker.md) | Aggregated financial summary used for reporting and monitoring. |

### Marketing

| Module | Definition |
|---|---|
| [mkt](./mkt/README.md) | Root marketing module grouping outreach and retention tools. |
| [mkt-campaign](./mkt/campaign.md) | Targeted messaging campaign sent to a segment of clients. |
| [mkt-referral](./mkt/referral.md) | Referral program tracking who referred whom and associated rewards. |
| [mkt-loyalty](./mkt/loyalty.md) | Points or reward system for repeat clients. |
| [mkt-promotion](./mkt/promotion.md) | Time-limited discount or special offer applied to catalog items. |
| [mkt-giftcard](./mkt/giftcard.md) | Prepaid value card redeemable against purchases. |

### Forms & documents

| Module | Definition |
|---|---|
| [form](./form/README.md) | Custom form definition attached to a workflow or intake process. |
| [form-question](./form/question.md) | Individual field or question within a form. |
| [form-response](./form/response.md) | A client's submitted answers to a form. |
| [document](./document/README.md) | File or document associated with a client or workspace. |
| [document-sign](./document/sign.md) | Signature request and status tracking for a document. |

### Resources & classification

| Module | Definition |
|---|---|
| [resource](./resource.md) | Physical or virtual asset (room, equipment) that can be reserved for bookings. |
| [taxonomy](./taxonomy.md) | Hierarchical category and tag system for classifying catalog items and clients. |

## Rules

- Every row carries `workspace_id`.
- Cross-module calls go through module APIs; never direct DB reads.
- AI modules compose platform `llm-action` with workspace context — they do not call providers directly.
