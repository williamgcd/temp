# 02-schedule / See the schedule

**Personas**: Sabriza (primary). Aplica igual a Júlia e Bianca.

## Scenario

Sabriza acorda, pega o café, abre o app. Quer saber três coisas, nessa ordem:

1. **O que tenho hoje?** — a pergunta de 90% das aberturas.
2. **O que tenho amanhã / essa semana?** — planejamento curto.
3. **O mês tá cheio ou vazio?** — sensação macro.

Essa use case cobre **ver** a agenda. Criar, remarcar, etc., são outras.

## Default view

Landing = **Lista agrupada por dia**, começando em **hoje**, scrollável pra baixo (até ~30 dias).

Por quê? O telefone tem tela vertical longa; lista dá muita densidade; Sabriza geralmente tem poucos atendimentos por dia, então lista cabe bem. Só quem tem muitos atendimentos por dia (Bianca) se beneficia mais de Dia/Semana.

A view persiste entre sessões. Sabriza troca uma vez, fica.

## View toggle

Botão/ícone visível no topo (canto superior direito). Ao tocar, abre um seletor com:

- **Lista** (default)
- **Dia**
- **Mês**

**Semana** fica fora do v1 — quase todas as necessidades de semana cabem em Lista (densa) ou Dia (zoom). Adicionamos se pedirem.

## View 1 — Lista (referência: screenshot Google Calendar "Schedule")

Layout:

- Cada dia é um **grupo** com header à esquerda: abreviação do dia da semana em caps (`QUA`) + número do dia grande abaixo. Se for **hoje**, o número fica dentro de um círculo (destacar).
- À direita do header, os cards empilhados verticalmente, um por booking.
- Card mostra, nessa hierarquia:
  - **Linha 1 (bold)**: nome do cliente + serviço (se existir). Ex: `Maria Clara — manicure`. Se não tem serviço: só nome.
  - **Linha 2 (caption)**: `HH:MM – HH:MM` (ou `HH:MM` se duração default) + local/observação curta.
  - **Avatar/iniciais** do cliente à direita (opcional).
- Cor do card por **status**: default (azul), confirmado (azul sólido), pendente (cinza), no-show (vermelho), cancelado (tachado/esmaecido).
- **Linha "agora"**: linha horizontal sutil cruzando o dia de hoje na posição temporal correta, separando passado de futuro.
- Bloqueios (`schedule-blocker`) aparecem como cards neutros (cinza/roxo), etiqueta discreta "Bloqueado".
- Feriados (`schedule-holiday`) aparecem como header especial do dia (ex: `FERIADO — Páscoa`).

Interações:

- **Tap no card** → booking detail sheet.
- **Long-press no card** → menu rápido (Remarcar / Cancelar / Concluir / Copiar / Adicionar nota).
- **Pull-to-refresh** no topo.
- **Scroll infinito** pra frente (até uns 60 dias). Botão "Ir pra data" no topo pra saltar.
- **FAB +** (canto inferior direito), sempre visível, abre bottom sheet Novo atendimento.

Empty states:

- Nenhuma booking: **Silvia em destaque** + CTA grande "Bora marcar seu primeiro atendimento?" (usar como empty state rico, não lista vazia triste).
- Dia vazio entre dias cheios: **ocultar** o grupo (não mostrar "sem atendimentos"). Se usuário fizer "Ir pra data" e cair num dia vazio, aí sim mostrar *"Dia livre — aproveita."*

## View 2 — Dia (referência: screenshot timeline estilo iOS Calendars)

Layout:

- **Week strip** no topo: 7 dias (S M T Q Q S S) com número abaixo. Hoje em círculo vermelho. Swipe lateral navega semana.
- Título do dia abaixo do strip: `Segunda — 6 Abr 2026` (data e dia por extenso).
- **Timeline vertical**: horas no eixo esquerdo (03:00, 04:00…); bookings como cards/barras verticais à direita, posicionadas e dimensionadas pela duração.
- **Indicador de "agora"**: linha horizontal vermelha cruzando a timeline, com pill `HH:MM` na borda esquerda.
- Card do booking na timeline mostra: cliente, duração (`HH:MM – HH:MM`), ícone de serviço se houver.
- Bloqueios: barras cinza com label "Bloqueado".
- Horário de atendimento (quando configurado): faixa de fundo sutil marcando o horário útil (ex: 9h-19h com fundo escuro mais claro). Antes/depois, fundo mais apagado.

Interações:

- **Tap** num card → detail sheet.
- **Long-press + drag** em slot vazio → criar booking naquele slot.
- **Drag** de card existente → remarcar (snap em `slot_granularity_minutes`).
- **Pinch zoom** (opcional v1.1) pra ajustar densidade vertical.
- Barra inferior: pill **"Hoje"** (scroll pro momento atual), atalho de view.

## View 3 — Mês (referência: screenshot grid mensal estilo iOS Calendar)

Layout:

- Título: ano pequeno no topo com back chevron, mês grande abaixo.
- Header com dias da semana abreviados.
- Grid 6 linhas × 7 colunas. Dia atual em círculo vermelho.
- Abaixo do número do dia: **pills de eventos** (cor = status ou serviço). Limite visível ~2-3; se tiver mais, mostrar `+N`.
- Feriados: pill dedicada no topo do dia com ícone de estrela.
- Dias vazios: só o número.

Interações:

- **Tap no dia** → navega pra **view Dia** naquele dia.
- **Swipe lateral** → mês anterior / próximo.
- Barra inferior: pill **"Hoje"**, atalho de view.

## Agent angle (Silvia)

### Reactive mode (tier Pro, R$ 9,99)

Silvia **não empurra** nada. Responde quando perguntada — no chat dedicado da Silvia ou num campo de busca global.

Perguntas típicas que Silvia responde sem sair do app:

- *"O que tenho amanhã?"* → lista estruturada com horário, cliente, serviço.
- *"Quanto atendi essa semana?"* → contagem de bookings + revenue parcial (se serviço+preço existem).
- *"Quando tenho 1h livre essa semana?"* → lista de slots livres a partir de agora.
- *"Próximos aniversariantes?"* → clientes com aniversário nos próximos 14 dias.
- *"Meu dia mais cheio?"* → a data e quantidade.
- *"Cadê a Maria?"* → último booking dela, próximo agendado, histórico resumido.

### Autonomous mode (tier Silvia, R$ 49,99)

Silvia **empurra** snapshots nos momentos chave, via chat interno + push notification (opt-in no OS).

- **Bom dia** (X min antes do primeiro atendimento, ou 8h se vazio): *"Bom dia! Hoje são 4 atendimentos. Primeiro às 14h (Maria — manicure). Tem 2h livres entre 16h30 e 18h30."*
- **Boa noite** (depois do último atendimento): *"Fim do dia. 4 atendimentos feitos, 1 no-show (Ana), R$ 280 recebidos. Amanhã começa às 14h com 6 agendados."*
- **Alerta de dia vazio** (D-2 de um dia < 30% da média): *"Quinta tá com só 1 horário marcado. Quer que eu ofereça pra suas 10 clientes mais frequentes?"*
- **Sobreposição inesperada** (quando alguém marca em cima): *"Notei que você marcou duas clientes às 15h quarta. Foi proposital (uma no forno) ou quer remarcar uma?"*

Todos esses snapshots caem no mesmo chat da Silvia; nunca interrompem com modal.

## Modules touched

**Primary**

- `_workspace/schedule-booking` — `list()` com filtros data/cliente/status é o endpoint principal.
- `_workspace/schedule` — working hours pra faixa "horário útil" no Dia; `available_slots` pra pergunta "quando tenho 1h livre".

**Support**

- `_workspace/schedule-blocker` — pra renderizar bloqueios.
- `_workspace/schedule-holiday` — pra marcar feriados.
- `_workspace/client` — nome e aniversário pros cards e buscas.
- `_workspace/catalog-service` — nome e cor do serviço.

**AI**

- `_workspace/ai-insight` — tópicos `daily_snapshot`, `evening_recap`, `empty_day_alert`.
- `_workspace/ai-action` — rules `morning_snapshot`, `evening_summary`, `empty_day_alert` (tier Silvia).
- `_workspace/ai-message` — não requerido aqui; usado só pelo alerta de dia vazio se Sabriza aceitar a sugestão.

**Internals**

- `_internal/cache` — query de booking list por workspace+date-range é quente, cachear agressivo.
- `_internal/track` — eventos `schedule_view_opened` com prop `view` (`list`, `day`, `month`), `schedule_view_changed`.

## Doc changes (lock from draft)

- ✅ `_workspace/schedule/booking.md` — já atualizado na use case `create-appointment` com `list()` API. Nenhuma mudança adicional.
- ✅ `_workspace/schedule/README.md` — manter `available_slots` como está.
- ⚠ `_workspace/ai/insight.md` — adicionar exemplos de `topic`:
  - `daily_snapshot` — resumo do dia.
  - `evening_recap` — fechamento do dia.
  - `empty_day_alert` — aviso antecipado de dia vazio.
  - `weekly_summary` — fechamento da semana.
- ⚠ `_workspace/ai/action.md` — já tem `morning_snapshot`, `evening_summary`, `empty_day_alert` na lista de exemplos. Manter.

## Open questions

- **Default view**: lista confirmada. Ok?
- **Week view**: fora do v1. Ok ou tem caso de uso que força?
- **Timezone**: default `America/Sao_Paulo` quando `workspace-config.timezone` não está setado. Ok (BR-only v1)?
- **Week starts on**: `workspace-config.week_starts_on` já existe. Default domingo ou segunda? Propondo **domingo** (padrão BR calendário). Confirmar.
- **Limite de pills no Mês**: 2-3 visíveis + `+N`? Ou número (ex: "5 atendimentos")? Propondo pills, com fallback a número só quando muito denso.
- **"Bom dia" horário**: X minutos antes do primeiro atendimento. X = 60? E se dia vazio, manda às 8h? Ou não manda?
- **Push notifications**: precisam opt-in no OS. Onde pedir? Propondo na primeira autonomous action pertinente, não em onboarding.
- **Referências visuais**: screenshots compartilhados pelo usuário (Google Calendar Schedule, iOS Calendars Day, iOS Calendar Month). Salvar imagens no repo ou manter só a descrição?
