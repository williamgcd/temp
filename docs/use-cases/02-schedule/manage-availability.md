# 02-schedule / Manage availability (working hours, blocks, days off)

**Personas**: Sabriza (primary).

Cobre **CRUD** de `schedule` (horário de atendimento), `schedule-blocker` (bloqueios pontuais) e `schedule-holiday` (feriados e fechamentos).

## Scenarios

- Sabriza define que atende **terça a sábado, das 14h às 20h**.
- Na quarta, tem consulta médica das 15h às 17h — bloqueia.
- Vai viajar de 23 a 30 de dezembro — marca feriado/folga.
- Toda quarta fecha pra almoço 12h-13h30.

## User journey (UI)

### Working hours

Entrada: **Agenda > Configurações > Horário de atendimento**, ou via Silvia quando ela sugere após observação.

Screen:
- Lista de dias (seg–dom). Cada um com toggle ligado/desligado.
- Dia ligado: define um ou mais intervalos (`09:00-12:00`, `14:00-18:00`). `+ Adicionar intervalo` por dia.
- Dia desligado: "Fechado".
- Footer: **Salvar**. Silvia avisa no chat se mudança afeta bookings já agendados fora do novo horário.

Primeiro uso: **preenchido pelo Google Places** (via onboarding) quando disponível. Sabriza só confirma.

Silvia (autonomous): após ~N bookings, detecta padrão → propõe horário padrão. *"Seus últimos 20 atendimentos foram todos entre 14h e 20h de terça a sábado. Quer salvar como seu horário padrão?"*

### Blockers (bloqueios pontuais)

Entrada: **long-press em slot vazio** na Day view, ou **FAB + > Bloquear horário**.

Bottom sheet:
- **Data** + **hora início** + **hora fim** (ou duração).
- **Motivo** (opcional; livre ou presets: `Almoço`, `Consulta`, `Pessoal`, `Manutenção`, `Folga`).
- **Recorrência** (opcional): `uma vez`, `toda semana nesse dia`, `todo mês`, custom (RRULE).
- Salvar.

Editar/deletar: tap no bloco na timeline → sheet idêntica com delete.

### Holidays / days off

Entrada: **Agenda > Configurações > Feriados e folgas**.

- Lista do ano: feriados nacionais (seed automático via região BR) + folgas custom.
- Toggle em cada feriado nacional: aplica ou ignora (e.g. Sabriza trabalha no Dia do Trabalho).
- `+ Adicionar folga`: período (data única ou faixa), nome, recorrência (`uma vez`, `todo ano`).

Silvia (autonomous): no dia 10 de janeiro, *"Quer importar os feriados nacionais de 2026?"*

## Agent angle (Silvia)

Same tools: `schedule.update_hours`, `schedule_blocker.create/update/delete`, `schedule_holiday.create/delete/seed`.

**Reactive**:
- *"Silvia, sexta que vem vou sair mais cedo — bloqueia das 17h em diante"* → cria blocker.
- *"Tô de férias de 20 a 27 de janeiro"* → cria holiday range.
- *"Qual meu horário de atendimento?"* → resumo + oferta de editar.

**Autonomous**:
- **Pattern detection**: 20 bookings consistentes → propõe working hours.
- **Year-start holiday seed**: janeiro → propõe importar feriados.
- **Conflict alert**: quando hours são alteradas, lista bookings afetados e pergunta o que fazer (remarcar, manter, avisar cliente).

## Modules touched

- `_workspace/schedule` — `update_hours`.
- `_workspace/schedule-blocker` — CRUD.
- `_workspace/schedule-holiday` — CRUD + `seed(region, year)`.
- `_workspace/ai-action` — `pattern_detect_working_hours`, `year_start_holiday_seed`, `hours_change_conflict_alert`.

## Doc changes

- ⚠ `_workspace/schedule/blocker.md` — listar presets comuns (`almoço`, `consulta`, `pessoal`, `manutenção`, `folga`) como `reason` sugeridos, não enum fixo.
- ⚠ `_workspace/schedule/holiday.md` — confirmar `seed(workspace_id, region, year)` já existente; documentar que BR é a região default v1.
- ⚠ `_workspace/ai/action.md` — adicionar rules `pattern_detect_working_hours`, `year_start_holiday_seed`, `hours_change_conflict_alert`.

## Open questions

- Blockers recorrentes via RRULE: parse completo no v1, ou só `weekly` / `monthly` / `yearly` simples? Propondo subset simples v1.
- Holiday é só dia cheio, ou permite faixa de horas (ex: "vou sair mais cedo nesse dia")? Propondo: **holiday = dia cheio**; meio-dia = blocker com recorrência.
- "Fechar por hoje" botão de emergência (ex: Sabriza adoeceu) — cancela/remarca tudo de hoje + avisa clientes. Fora do v1? Propondo sim mas pensar.
