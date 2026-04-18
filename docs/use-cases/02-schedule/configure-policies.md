# 02-schedule / Configure policies (regras da agenda)

**Personas**: Sabriza (primary); políticas mais estritas valem especialmente pra Bianca.

Cobre configuração de **todas** as regras de agenda: lembretes, janelas, fees, depósito, buffers, granularidade, duração default, regra de sobreposição.

## Scenario

Sabriza teve 3 no-shows no mês. Abre Silvia no chat: *"tem muita cliente sumindo"*. Silvia responde: *"Vi 3 no-shows esse mês. Quer começar a cobrar multa de R$ 20? Eu aviso as clientes na confirmação."*

Esse é o caminho certo pra config: **Silvia sugere; Sabriza aceita → ela faz o change via UI ou Silvia faz direto.**

Mas a tela de config também existe pra quem quer mexer direto.

## User journey (UI)

### Entry points

1. **Agenda > Configurações > Regras** — acesso direto.
2. **Silvia sugere** no chat → botão "Abrir configurações" leva direto à seção relevante.
3. Dentro do detalhe de um `catalog-service`: aba "Regras dessa modalidade" (override do default).

### Screen: Regras da agenda

Lista de seções colapsáveis, cada uma com os campos correspondentes:

**Lembretes**
- Toggle "Enviar lembrete via WhatsApp".
- Quantas horas antes (slider/picker: 2h, 4h, 24h, 48h, custom).
- Template do lembrete (opcional, com preview + variáveis `{cliente}`, `{data}`, `{hora}`, `{serviço}`).
- Por serviço? link pra overrides.

**Janela de agendamento**
- Antecedência mínima (client pode marcar com no mínimo X horas de antecedência). Default null = sem mínimo.
- Antecedência máxima (até X dias no futuro). Default null = sem máximo.

**Remarcação**
- Janela (até X horas antes, pode remarcar sem multa).
- Multa de remarcação fora da janela (R$).

**Cancelamento**
- Janela.
- Multa.

**No-show**
- Grace period (minutos após `ends_at` pra marcar automático como no-show).
- Multa.

**Depósito**
- Toggle "Exigir depósito no agendamento".
- Valor (fixo ou % do serviço).

**Buffers**
- Antes (minutos).
- Depois (minutos).

**Slots**
- Granularidade (15/30/60 min).
- Duração default quando não há serviço.
- Sobreposição permitida (toggle).

Rodapé: **Salvar**. Muda → emite `schedule_policy.changed`.

### Per-service override

Em `catalog-service` detail, aba **Regras dessa modalidade**. Mesmos campos com um toggle "Usar padrão da agenda" em cada. Desligar o toggle habilita o input com valor local.

## Agent angle (Silvia)

Same tools: `schedule_policy.patch`, `schedule_policy.set_override`.

**Reactive**:
- *"Silvia, muda a antecedência mínima pra 4h"* → faz `patch`.
- *"Quais são minhas regras?"* → lista amigável.

**Autonomous — detection & suggestion** (não aplica sem confirmação):
- **No-show pattern** (>2 em 30d) → sugere multa no-show.
- **Short-notice bookings gerando conflito** (ex: cliente marcou pra daqui 30min e Sabriza não viu a tempo) → sugere antecedência mínima.
- **Reagendamentos frequentes** por mesma cliente → sugere multa de remarcação.
- **Serviço específico com muito no-show** → sugere override só nele (ex: micropigmentação R$ 100 multa).
- **Lembrete mais cedo** se clientes esquecem: *"Notei que quem recebe lembrete 2h antes está vindo mais. Quer mudar default de 24h pra 2h?"*

## Modules touched

- `_workspace/schedule-policy` — todos os endpoints.
- `_workspace/catalog-service` — override por serviço.
- `_workspace/ai-insight` — gera as observações que viram sugestões.
- `_workspace/ai-action` — rules `suggest_no_show_fee`, `suggest_min_advance_window`, `suggest_reschedule_policy`, `suggest_reminder_timing`.

## Doc changes

- ⚠ `_workspace/schedule/policy.md` — já tem todos os campos; adicionar nota na intro: "UI de settings vive em Agenda > Regras; serviços individuais podem override em Catálogo > Serviço > Regras."
- ⚠ `_workspace/catalog/service.md` — adicionar nota: "Pode override qualquer campo de `schedule-policy` via `schedule_policy.set_override`."
- ⚠ `_workspace/ai/action.md` — adicionar rules de sugestão listadas acima.

## Open questions

- Templates de lembrete: liberdade total (livre) ou seleção de templates pré-definidos pela Silvia? Propondo híbrido: Silvia sugere 3; Sabriza pode customizar.
- Multa é cobrada **automaticamente** no Silvia-tier ou sempre requer ok? Propondo: sempre ok v1 (risco alto, reversibilidade baixa).
- Depósito vai exigir integração de pagamento antes do booking existir. Fora do v1? Propondo: deposit config existe, mas só no v2 quando `finance-payment` + link de pagamento estiverem prontos.
