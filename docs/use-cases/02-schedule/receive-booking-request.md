# 02-schedule / Receive booking request (self-serve link + WhatsApp)

**Personas**: Sabriza (primary). Fluxo chave pra reduzir tempo gasto em WhatsApp.

Cobre **inbound**: cliente quer marcar, **não é Sabriza criando** a booking. Dois canais: link público (self-serve) e WhatsApp (Silvia-tier).

## Scenarios

- **Self-serve**: Sabriza compartilha `sabriza.app.com/marcar` no Instagram bio. Maria clica, escolhe serviço e horário, confirma.
- **WhatsApp (Silvia-tier)**: Maria manda *"consegue me encaixar quarta?"* no WhatsApp conectado. Silvia responde com slots, confirma, cria booking.
- **Chat AI**: Sabriza abre Silvia e pede *"marca a Maria quarta 15h"*. (Já coberto em [create-appointment](./create-appointment.md) como variante.)

## Flow — Self-serve link

### Página pública

URL: `https://{hostname}/b/{workspace_slug}` ou `/b/{workspace_slug}/{service_slug}`.

Layout:
1. Banner do workspace (nome, foto, categoria — do onboarding Google Places).
2. Seletor de serviço (lista de `catalog-service` com `visibility = public` ativo).
3. Seletor de data (mês com days disponíveis destacados; vindo de `schedule.available_slots`).
4. Seletor de hora (slots livres pra aquele serviço + dia).
5. Form do cliente: **nome** (obrigatório), **WhatsApp** (obrigatório pra confirmar), **observação** (opcional).
6. Botão **Confirmar solicitação**.
7. Tela de sucesso: *"Pedido de horário enviado pra Sabriza. Você vai receber confirmação no WhatsApp em breve."*

**Status ao criar**: `pending_confirmation`. Sabriza **não aparece** como já-agendada; precisa aprovar.

### App side

Sabriza recebe notificação (push/chat): *"Maria pediu quarta 15h — manicure. Aprovar?"*

- No Inbox da agenda: card com `[Aprovar] [Sugerir outro horário] [Recusar]`.
- Aprovar → status `booked` → Silvia manda confirmação no WhatsApp.
- Sugerir outro → abre picker; envia contra-proposta.
- Recusar → status `cancelled` (motivo `declined`); mensagem educada enviada.

## Flow — WhatsApp (Silvia-tier)

### Detection

Evento `whatsapp.message_received` entra no bus. Silvia (via `ai-action` rule `inbound_whatsapp_booking`) analisa:

- Intent: `book | reschedule | cancel | confirm | question | out_of_scope`.
- Se `book`: extrai service hint + date/time hint.

### Silvia executa

1. Consulta `schedule.available_slots`.
2. Propõe no WhatsApp: *"Oi Maria! Tenho quarta 15h ou quinta 10h. Qual prefere?"*
3. Cliente responde.
4. Silvia cria `schedule_booking` com `source = whatsapp_ai` e `status = pending_confirmation`.
5. **Notifica Sabriza no app**: card idêntico ao self-serve (aprovar/sugerir/recusar). Ao aprovar, Silvia confirma com cliente no WhatsApp.

### Edge cases

- Cliente manda mensagem ambígua → Silvia pergunta clarificação (máx 2 rounds; depois escala pra Sabriza).
- Cliente pede horário fora de disponibilidade → Silvia propõe adjacentes.
- Cliente não responde → Silvia tenta uma vez depois de 2h; senão escala pra Sabriza.
- Intent `out_of_scope` (e.g. preços de produtos que não existem) → encaminha pra Sabriza.

## Agent angle

Reactive vs autonomous **diferente** aqui porque o inbound é WhatsApp:
- **Pro tier**: link público funciona; WhatsApp inbound **não** é processado (Silvia não tem canal). Sabriza responde manual e cria no app.
- **Silvia tier**: tudo acima; Sabriza só supervisiona.

Both tiers: **Sabriza nunca é pulada**. Aprovação é sempre dela pelo menos na primeira interação; pode flexibilizar depois (ver open questions).

## Modules touched

- `_workspace/schedule` — `available_slots` (usado na página pública e na proposta da Silvia).
- `_workspace/schedule-booking` — `create` com `status = pending_confirmation`, `source = self_serve | whatsapp_ai`; `confirm` na aprovação.
- **[NEW] `_workspace/schedule-public-link`** — public-facing link + token management, workspace slug, service slug.
- `_workspace/catalog-service` — `visibility = public` flag.
- `_workspace/client` — `find_or_create` pelo WhatsApp ou nome + WhatsApp no booking request.
- `_workspace/ai-message` — draft/send de confirmação via WhatsApp.
- `_workspace/ai-action` — rule `inbound_whatsapp_booking` (e variações reschedule/cancel).
- `_internal/token` — assinatura do link público se precisar ACL.

## New module: `schedule-public-link`

Precisa existir. Proposta:

### Purpose

Exposição pública e segura da disponibilidade e do form de solicitação de booking.

### Data model

- `schedule_public_link`: `workspace_id` (pk), `slug`, `enabled`, `services_visible: string[] | null`, `intro_text?`, `updated_at`.
  - Apenas **uma** link pública por workspace v1. `slug` único global.
  - Se `services_visible` é null, todos os serviços `public` aparecem; senão só os listados.

### Public API

- `schedule_public_link.enable(workspace_id, slug)` / `disable(workspace_id)`
- `schedule_public_link.get_by_slug(slug)` — endpoint público.
- `schedule_public_link.available_slots({ slug, service_slug?, date_range })` — wrapper sobre `schedule.available_slots` com filtros públicos.
- `schedule_public_link.request_booking({ slug, service_slug, starts_at, client_name, client_phone, notes? })` — cria booking em `pending_confirmation`.

### Events emitted

- `schedule_public_link.enabled`, `schedule_public_link.disabled`, `schedule_public_link.booking_requested`.

### Depends on

- `_workspace/schedule`, `_workspace/schedule-booking`, `_workspace/catalog-service`, `_workspace/client`.
- `_internal/audit`.

### Consumed by

- Public HTTP edge (o site público `/b/{slug}`).
- Workspace settings UI ("Agenda > Link de agendamento").

## Doc changes

- ✅ **Criar** `_workspace/schedule/public-link.md` com o conteúdo acima.
- ⚠ `_workspace/schedule/README.md` — listar `schedule-public-link` nos submódulos.
- ⚠ `_workspace/README.md` — adicionar `schedule-public-link` na tabela de Schedule.
- ⚠ `_workspace/catalog/service.md` — confirmar/adicionar `visibility` (`public` / `private`) no data model se ainda não estiver.
- ⚠ `_workspace/schedule/booking.md` — confirmar `status = pending_confirmation` e `source = self_serve | whatsapp_ai`.
- ⚠ `_workspace/ai/action.md` — manter `inbound_whatsapp_booking` e adicionar `inbound_whatsapp_reschedule`, `inbound_whatsapp_cancel`.

## Open questions

- Slug do link público: gerado do workspace name? Sabriza pode customizar? Propondo: sugerido + editável.
- Auto-aprovar bookings da página pública se horário está dentro do working hours e sem conflito? Propondo **não no v1** (toda booking passa por `pending_confirmation`). Flag `ai.auto_approve_public_bookings` no tier Silvia em v1.1.
- Link público expira? Propondo: vive enquanto `enabled`.
- Captcha / anti-spam na página? Propondo simples: rate limit por IP + phone verification via WhatsApp opcional.
- WhatsApp provider: Meta Cloud API (oficial) ou via BSP tipo Z-API? Decisão arquitetural separada; ambos plugam no `ai-message.channel = whatsapp`.
- Mensagem de confirmação da Silvia: tem template default ou é sempre gerada por LLM? Propondo LLM com persona, capada por length.
