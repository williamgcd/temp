# 02-schedule / Encaixe (walk-in / squeeze in)

**Personas**: Sabriza e Júlia principalmente (Bianca raramente tem walk-in).

Variante do create com urgência — cliente já está ali ou quer hoje, precisa caber.

## Scenario

Sabriza está terminando uma cliente. Outra chega e pergunta: *"dá pra me atender hoje?"* Sabriza abre o app, quer ver o próximo horário possível em 10 segundos.

Ou: mensagem no WhatsApp *"tem como me encaixar hoje?"* e Silvia precisa propor.

## User journey (UI)

### Encaixe button

FAB expandido tem opções: **+ Atendimento** / **+ Encaixe** / **+ Bloquear horário**.

**+ Encaixe** abre um fluxo mais curto que o criar normal:

1. **Quem** — autocomplete de clientes (last seen first).
2. **Próximo horário livre** — Silvia/sistema sugere imediatamente o próximo slot disponível com duração default (ou do último serviço daquela cliente). Card grande com o slot sugerido + `[Confirmar]`.
3. Abaixo: alternativas (próximos 3 slots). Tap em qualquer → confirma.
4. Se **nenhum slot** cabe hoje: opção *"Encaixar mesmo assim?"* — sobrepõe, respeitando `overlap_allowed`.

Um tap pra confirmar → booking criado com `source = admin` (ou `chat_ai` se Silvia fez). Agenda atualiza.

### Overlap permitido

Se `schedule-policy.overlap_allowed = true` (default), Silvia/sistema oferece tanto slots livres quanto "pode encaixar às 15h junto com a Maria (enquanto ela estiver no processo de química)".

## Next-free-slot API

Precisa de um helper no `schedule` pra isso ser rápido.

```
schedule.next_free_slots({
  workspace_id,
  duration_minutes,
  after?: datetime,
  count?: number,   // default 3
  allow_overlap?: boolean
}) → [{ starts_at, ends_at, warnings? }]
```

Respeita working hours, blockers, holidays, policy.

## Agent angle (Silvia)

Same tools: `schedule.next_free_slots`, `schedule_booking.create`.

**Reactive**:
- *"Silvia, quando tem 1h livre hoje?"* → usa `next_free_slots` com after=now.
- *"Silvia, encaixa a Maria agora"* → propõe + confirma + cria.

**Autonomous (Silvia-tier)** — inbound WhatsApp:
- Cliente: *"tem como hoje?"* → Silvia detecta intent `book` com urgency flag → chama `next_free_slots(after=now)` → propõe o próximo no WhatsApp: *"Tenho 16h30 livre. Te encaixo?"*
- Se cliente confirma → cria booking `pending_confirmation`, notifica Sabriza → ao aprovar, Silvia confirma na conversa.

Urgency detection é só um signal; aumenta prioridade mas não pula aprovação.

## Modules touched

- `_workspace/schedule` — novo `next_free_slots`.
- `_workspace/schedule-booking` — `create` com soft overlap.
- `_workspace/schedule-policy` — `overlap_allowed`, `slot_granularity_minutes`.
- `_workspace/client` — find_or_create; lookup de "último serviço" pra pre-selecionar duração.
- `_workspace/ai-action` — detection `urgency_inbound` piggyback em `inbound_whatsapp_booking`.

## Doc changes

- ⚠ `_workspace/schedule/README.md` — adicionar `next_free_slots` na Public API.
- ⚠ `_workspace/schedule/booking.md` — nenhuma mudança (já suporta overlap soft).
- ⚠ `_workspace/ai/action.md` — referenciar urgency flag em `inbound_whatsapp_booking`.

## Open questions

- Duração default do encaixe quando cliente não tem histórico? Usa `schedule-policy.default_duration_minutes` (60min). Ok?
- "Próximo slot" cruza meio-dia / intervalo de almoço? Propondo **respeitar blockers recorrentes** sempre; oferecer overlap só com **outras bookings**, não com blockers.
- Se nenhum slot cabe hoje nem amanhã: Silvia oferece depois de amanhã? Propondo sim, até 7 dias.
