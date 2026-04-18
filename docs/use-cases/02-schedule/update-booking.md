# 02-schedule / Update booking (reschedule, cancel, no-show, complete, edit)

**Personas**: Sabriza (primary). Aplica igual a Júlia e Bianca.

Cobre **U + D** do CRUD do booking. Create está em [create-appointment](./create-appointment.md); Read em [see-the-schedule](./see-the-schedule.md).

## Scenarios

- **Remarcar**: Maria pediu pra trocar pra sexta às 16h.
- **Cancelar**: cliente avisou que não vem.
- **Editar detalhes**: Sabriza quer trocar o serviço de "manicure" pra "manicure + pedicure", ou adicionar uma observação.
- **Concluir**: atendimento acabou, marca como concluído (habilita cobrança/invoice).
- **No-show**: cliente não apareceu.

Deletar de verdade **não existe**. Audit e financeiro precisam do histórico. "Deletar" = cancelar com motivo (`deleted`).

## User journey (UI)

### Entry point

Em qualquer view (Lista/Dia/Mês), **tap no card do booking** abre o **booking detail sheet** (bottom sheet full-height):

- Header: cliente + avatar + status badge.
- Linha de horário: `quarta 15h — 16h` com ícone de calendário; tap abre date/time picker pra **remarcar inline**.
- Serviço: chip editável; tap abre seletor/autocomplete sobre `catalog-service`; pode limpar pra voltar a "sem serviço".
- Duração: chip editável se serviço é nulo; senão segue duração do serviço (editável com override).
- Notas: textarea expansível.
- Seção **Pagamento** (condicional): vínculo com `finance-invoice` quando existir.
- Ações no rodapé:
  - **Concluir** (primária quando `starts_at` já passou)
  - **Cancelar** (destrutiva) — com seletor de motivo.
  - **No-show** (aparece só depois de `ends_at` passar sem concluir).
  - **Confirmar** (quando status = `pending_confirmation`).
- Menu overflow: **Copiar atendimento**, **Remarcar pra outro dia**, **Deletar** (usa cancel com motivo `deleted`).

### Drag to reschedule (Dia view)

- Long-press no card da timeline → card flutua → solta em novo slot (snap em `slot_granularity_minutes`).
- Libera → confirma com toast "Movido pra 16h — Desfazer" (undo por 5s).

### Swipe actions (Lista view)

- Swipe à direita: **Concluir** (verde).
- Swipe à esquerda: **Cancelar** (vermelho).
- Ambos com confirmação antes de aplicar.

## State transitions

```
booked ─┬─ reschedule ─→ booked (new time)
        ├─ confirm ────→ confirmed
        ├─ complete ───→ completed
        ├─ cancel ─────→ cancelled
        └─ no_show ────→ no_show

pending_confirmation ─┬─ confirm ──→ confirmed
                      └─ cancel ───→ cancelled

confirmed ─┬─ reschedule → confirmed (new time, keeps confirmed)
           ├─ complete ─→ completed
           ├─ cancel ───→ cancelled
           └─ no_show ──→ no_show

completed / cancelled / no_show: terminal.
```

Edit de campos (service, duration, notes) não muda status.

## Cancellation policy interaction

Quando `schedule-policy` tem `cancellation_window_hours > 0` e `cancellation_fee_cents > 0`:

- Cancel dentro da janela sem fee: segue normal.
- Cancel fora da janela: bottom sheet pergunta *"Cobrar multa de R$ X?"* → se sim, cria `finance-invoice` com linha "Multa de cancelamento".
- Default v1 = 0/0, nenhum prompt aparece.

No-show segue mesma lógica com `no_show_fee_cents`.

## Agent angle (Silvia)

**Same tools**: `schedule_booking.reschedule / cancel / complete / no_show / update`. Sabriza dispara via UI; Silvia dispara via detecção.

**Reactive mode**:
- *"Silvia, remarca a Maria pra sexta 16h"* → valida disponibilidade, faz `reschedule`, confirma.
- *"Cancela o horário da Ana"* → pede confirmação, faz `cancel` com motivo.
- *"Anota que a Maria quer franja"* → faz `update` em `notes`.

**Autonomous mode**:
- **Auto-complete** (detect): booking cujo `ends_at` passou há > 30min e ainda em `booked`/`confirmed` → candidato a `complete`. Silvia pergunta antes em v1.
- **No-show detection**: booking com `ends_at` passado há > `no_show_grace_minutes` (policy) sem interação → propõe `no_show`.
- **Reschedule via WhatsApp inbound**: cliente manda "consegue mudar pra sexta?" → Silvia propõe slot, executa `reschedule` ao ok.
- **Cancel via WhatsApp inbound**: cliente avisa "não vou poder" → Silvia `cancel` e, se fora da janela + fee > 0, pergunta se cobra.

## Modules touched

- `_workspace/schedule-booking` — `reschedule`, `cancel`, `confirm`, `complete`, `no_show`, `update`.
- `_workspace/schedule` — `is_bookable` na validação do novo horário.
- `_workspace/schedule-policy` — janelas e fees.
- `_workspace/finance-invoice` — cria linha de multa quando aplicável.
- `_workspace/ai-action` — rules `auto_complete_suggestion`, `no_show_detection`, `inbound_reschedule`, `inbound_cancel`.
- `_workspace/ai-message` — dispara comunicação com cliente em remarca/cancel.

## Doc changes

- ⚠ `_workspace/schedule/booking.md` — adicionar método `schedule_booking.update(id, patch)` pra edit de `service_id | duration | notes` sem mudar status. Adicionar motivo `deleted` ao `cancel`.
- ⚠ `_workspace/schedule/policy.md` — adicionar campo `no_show_grace_minutes` (default 30).
- ⚠ `_workspace/ai/action.md` — adicionar rules `auto_complete_suggestion`, `no_show_detection`, `inbound_reschedule`, `inbound_cancel` nos exemplos.

## Open questions

- Drag-to-reschedule no Mês (não-trivial): fora do v1? Propondo sim.
- Editar cliente de uma booking existente: raro e fonte de bugs. Propondo **não permitir** — "cancele e crie novo" (com "Copiar atendimento" pra facilitar).
- Undo de cancel: janela de 5s no toast, ou permanente até a sessão fechar?
- Notificar o cliente automaticamente em remarca/cancel? Propondo: **sempre draft** (mesmo em Pro). Enviar = manual em Pro, auto em Silvia-tier se `ai.auto_send_whatsapp`.
