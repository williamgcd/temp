# 02-schedule / Create appointment

**Personas**: Sabriza (primary). Aplica igual a Júlia e Bianca.

## Scenario

Terça, 11h. Sabriza recebe mensagem no WhatsApp da Maria Clara: *"amiga, consegue me encaixar amanhã à tarde pra fazer a unha?"* Sabriza já tem um horário em mente — amanhã 15h está livre. Pra não esquecer, abre o app e marca.

Esse é **o loop principal do produto**. Tem que ser: abrir → 3 toques → feito.

## User journey (UI)

1. Sabriza abre o app. Landing = agenda, view = lista de próximos atendimentos. FAB **+ Adicionar** proeminente.
2. Toca o FAB. Abre bottom sheet "Novo atendimento" com **3 campos**:
   - **Cliente** — input com autocomplete sobre `_workspace/client`. Se Maria Clara existe, sugere. Se não, digitar o nome e pronto (telefone opcional).
   - **Data** — default "hoje"; atalhos visíveis: `hoje / amanhã / próxima [dia-da-semana-do-último-booking-dessa-cliente]`. Tap no campo abre date picker.
   - **Hora** — default = próximo slot razoável (próximo horário redondo após agora, ou 9h se for de manhã cedo); picker estilo roda.
3. Toca **Salvar**. Bottom sheet fecha. Booking aparece na agenda com animação curta. Toast na base: *"Atendimento salvo — Ver detalhes"*.
4. Opcional: link inline no toast *"Adicionar lembrete"* que abre config de lembrete para aquele booking.

Campos escondidos atrás de um **"Mais detalhes" colapsado**: serviço, duração, preço, observação, lembrete custom. Default = colapsado, 0 toques necessários.

## Agent angle (Silvia)

### Reactive mode (tier Pro, R$ 9,99)

Silvia **não interrompe** o fluxo de criação. Depois que o booking é salvo, se for o **primeiro atendimento de um cliente**, Silvia aparece **no chat** (não modal, não alerta) com:

> *"Anotei Maria Clara quarta às 15h. Quer adicionar o telefone dela pra eu poder mandar lembrete?"*

Sabriza pode responder ali mesmo (*"11999998888"*) e Silvia atualiza o registro do cliente via `_workspace/client`.

Outras perguntas que Silvia pode fazer **ao longo do tempo**, **uma por booking** (nunca mais de uma por vez), em ordem de relevância:

- *"Foi manicure? Quer que eu salve como o serviço padrão da Maria?"*
- *"Quanto você cobra dessa manicure? Posso colocar R$ 50?"*
- *"Notei que você atendeu 4 vezes entre 14h e 17h essa semana — quer marcar como horário padrão?"*

### Autonomous mode (tier Silvia, R$ 49,99)

A mensagem da Maria Clara cai no WhatsApp conectado à conta. Silvia lê ("*encaixar amanhã à tarde*"), consulta a agenda, propõe no chat do app:

> *"Maria Clara quer se encaixar amanhã à tarde. Tem 15h e 16h livres. Posso marcar 15h?"* `[✓ Sim] [✗ Escolher outro]`

Sabriza toca ✓. Silvia responde no WhatsApp da Maria: *"Marquei pra amanhã 15h, confirma?"* Ao receber `SIM`, cria o booking com `status = confirmed`. Se não confirma em 2h, Silvia pergunta a Sabriza antes de cancelar.

### Natural-language inbound (ambos os tiers)

> *"Silvia, marca a Maria amanhã 15h"*

Silvia cria o booking. Mesmo fluxo do manual, executado por ela.

## Modules touched

**Primary**

- `_workspace/schedule-booking` — novo método `create_minimal`.
- `_workspace/client` — `find_or_create` apenas com `name`.

**Support**

- `_workspace/schedule` — availability check *soft* (avisa se conflita, não bloqueia).
- `_workspace/schedule-policy` — checa `overlap_allowed`, `slot_granularity_minutes`.
- `_workspace/catalog-service` — não requerido v0.

**AI**

- `_workspace/ai-persona` (Silvia) — persona default.
- `_workspace/ai-message` — tier Silvia: draft/send WhatsApp.
- `_workspace/ai-action` — tier Silvia: rule `inbound_whatsapp_booking`.

**Internals**

- `_internal/audit` — `booking.created` auditado.
- `_internal/track` — evento `booking_created` com props `source`, `lead_time_hours`, `has_phone`, `has_service`.

## Doc changes (lock from draft)

- ✅ `_workspace/schedule/booking.md` — atualizar:
  - Campos obrigatórios v0: `client_id`, `starts_at`.
  - Opcionais: `service_id`, `ends_at` (inferido ou default 60min), `notes`.
  - Novo método: `schedule_booking.create_minimal({ workspace_id, client_name | client_id, starts_at, duration_minutes? })`.
  - Evento `booking.created` carrega `source` ∈ `admin | whatsapp_ai | self_serve | import | chat_ai`.

- ✅ `_workspace/client/README.md` — confirmar que `create` aceita apenas `{ name }` (phone, email nulos ok).

- ✅ `_workspace/schedule-policy.md` — **criar** (novo módulo no draft, lock agora). Campos iniciais:
  - `overlap_allowed: boolean` (default `true`)
  - `slot_granularity_minutes: number` (default `30`)
  - `buffer_before_minutes`, `buffer_after_minutes` (default `0`)
  - `default_duration_minutes` (default `60`)
  - `advance_booking_min_hours`, `advance_booking_max_days` (v1: null / null)
  - Per-service override map.

- ⚠ `_workspace/ai-action.md` — adicionar rule kind `inbound_whatsapp_booking` nos exemplos.
- ⚠ `_workspace/ai-message.md` — adicionar intent `booking_confirmation`.

## State transitions

`schedule_booking.status`:

- `booked` — default quando criado manualmente por Sabriza.
- `pending_confirmation` — cliente pediu via self-serve ou WhatsApp, aguardando ack.
- `confirmed` — ambos os lados confirmaram (Sabriza → `booked`, cliente respondeu SIM).
- `completed` — atendimento realizado.
- `cancelled` — cancelado por qualquer lado.
- `no_show` — cliente não veio.

**V0**: manual create → `booked`. Estados `pending_confirmation` e `confirmed` ganham sentido quando ligamos Silvia ao WhatsApp.

## Open questions

- **Duração default quando não tem serviço**: propondo **60min**. Silvia reaprende por observação (após 10 atendimentos sem serviço, média das durações).
- **Booking sem serviço na UI**: qual label mostrar? Propondo o nome do cliente como label principal + badge discreto *"sem serviço"* se Silvia ainda não perguntou.
- **Horário fora do "horário de atendimento"** (quando configurado): warn soft ou nada? Propondo warn soft com opção de "atender fora do horário" sem desabilitar o save.
- **Primeira tela do app sem nenhuma booking**: agenda vazia com CTA grande + Silvia no chat introdutória? Propondo isso (empty state rico, não lista vazia triste).
- **Autocomplete de cliente**: mostrar últimos 5? Rankeado por frequência? Ordem alfabética?
