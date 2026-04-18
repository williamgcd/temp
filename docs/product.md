# Product

Living doc for product-level decisions: audience, positioning, principles, pricing, onboarding, and the product's AI assistant. Updated as we iron things out.

## Audience

Beauty professionals in Brazil. V1 focuses on **solo / self** pros (see [personas](./personas.md)):

- Primary: **Sabriza** — atende em casa.
- Secondary: **Júlia** — aluga cadeira em salão.
- Tertiary: **Bianca** — estúdio próprio solo.

Multi-pro (salão com equipe) is an expansion path via `_platform/workspace-member` and `_platform/workspace-relate`, not v1.

## Positioning

> Enterprise-level tools for solo/small beauty professionals, with an AI assistant that helps them learn about their own business.

## Silvia — the AI assistant

**Silvia** is the product's default AI persona. Every workspace ships with her as the default `_workspace/ai-persona` record. Workspaces may rename or retune her; "Silvia" is the brand-level default we design and market around.

Silvia has two modes, gated by plan:

- **Reactive mode** (Pro tier): she answers when Sabriza asks. She may *draft* actions (reminders, replies, invoices) but never *executes* without a tap.
- **Autonomous mode** (Silvia tier): she observes triggers and executes pre-approved actions within guardrails. Sabriza supervises.

## Design principles

### 1. Progressive disclosure via Silvia

The app never makes Sabriza configure upfront. She does the minimum to accomplish her goal; Silvia observes usage and asks for the missing piece *when it matters*. Settings screens exist as destinations Silvia can link to — not required on-ramps.

Applies to **configuration**.

### 2. Two tiers, one product

Same codebase, same modules. The difference is **who pulls the trigger**.

- **R$ 9,99 — Pro tools**: Sabriza runs her business; the app gives her clarity and control. Silvia is reactive.
- **R$ 49,99 — Silvia runs it**: autonomous mode. Silvia handles WhatsApp bookings, reminders, reconciliação de maquininha, campanhas, follow-up de no-show, lembrete de MEI, sugestão de preço. Sabriza supervises, não executa.

Applies to **execution**.

### Relationship between the two

Progressive disclosure governs *configuration* (both tiers). The tier motto governs *execution* (who acts). Config grows for everyone; execution is what upgrades buy.

## Pricing (v1)

| Plan | Price | Summary |
|---|---|---|
| `pro` | R$ 9,99 / mês | Full feature set, reactive Silvia. |
| `silvia` | R$ 49,99 / mês | Everything in Pro + autonomous Silvia. |
| `trial` | 14 days free (TBD) | Silvia-tier trial, auto-downgrade to Pro unless upgraded. |

Implemented via `_platform/plan` + `_platform/plan-subscription`. Example feature flags:

- `ai.autonomous` — autonomous actions allowed.
- `ai.auto_send_whatsapp` — send messages without human confirmation.
- `ai.proactive_insight` — insights pushed, not pulled.
- `ai.payout_reconcile` — auto-match maquininha settlements to bank.
- `ai.tax_reminder` — proactive MEI/DAS reminders.

More flags added as modules land.

## Onboarding

Minimum friction, maximum signal. Two inputs, Google fills the rest.

1. **Seu nome** — *"Como você quer ser chamada?"* → saved to user profile.
2. **Nome do seu negócio + cidade** — *"Onde você atende?"* → Google Places / Business Profile lookup.

If a match is found, show **one confirmation screen** with:

- Nome do negócio
- Endereço
- Telefone
- Instagram (if linked on the Google profile)
- Horário de funcionamento
- Categoria
- Fotos
- Às vezes: serviços e preços de referência

Sabriza confirms or edits. If **no match**, fallback to a manual form with the same fields.

After either path, Silvia greets in chat:

> *"Oi, sou a Silvia, te ajudo a organizar sua agenda. Pode ir criando que eu pergunto o que precisar."*

**Not collected at onboarding**: working hours (past the Google-fetched default), services, prices, payment methods, tax regime. Silvia picks these up via observation.

## Core loop

Open app → land on agenda → tap **+ Adicionar atendimento** → three fields (cliente, data, hora) → done.

Everything else (serviço, preço, lembrete, política) is Silvia-asked or settings-driven later.

## Open product questions

- **Trial**: 14 days of Silvia-tier with auto-downgrade to Pro? Require card upfront? Or trial without card?
- **Free tier**: default position is no free tier. Possibly a read-only "observador" mode to reduce onboarding regrets.
- **Silvia-tier overage**: is there a ceiling on autonomous actions per month? Or unlimited at R$ 49,99?
- **Language**: pt-BR only v1. English / es-LATAM is post-v1.
- **Country**: Brazil only v1. BRL only. LATAM expansion post-v1.
- **Billing cadence**: monthly only, or annual with discount?
- **Google API**: Places API cost (~US$17/1000 requests) acceptable; any PII/privacy constraints to confirm.
