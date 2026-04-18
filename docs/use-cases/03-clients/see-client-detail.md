# 03-clients / See client detail

**Personas**: Sabriza (primary). Alto valor pra Bianca (clientes de ticket alto com histórico longo).

**R** num cliente específico — o hub completo.

## Scenario

Maria está chegando pra atendimento. Sabriza abre o perfil rapidinho: *"da última vez ela tinha pedido franja, quero confirmar."* Quer ver num vistaço: quem é, histórico, notas, pacote ativo, se deve algo.

## User journey (UI)

Entrada: tap num item da lista de clientes, ou do card no detail de um booking.

Layout (scrollável):

### Header

- Avatar/iniciais grande à esquerda.
- Nome em destaque.
- Chips de contato: **WhatsApp**, **Ligar** (taps abrem app externo).
- Menu overflow: **Editar**, **Arquivar**, **Bloquear**, **Mesclar com outra…**, **Ver todas as mensagens da Silvia**.

### Silvia card (top)

Resumo de uma linha, gerado por `ai-insight`:

> *"Maria vem em média a cada 28 dias; última visita foi há 32. Gastou R$ 420 nos últimos 90 dias. Tem pacote de 5 manicures (2 usadas)."*

Tap abre uma conversa com Silvia contextualizada no cliente.

### Stats strip

Cards horizontais scrolláveis:

- Atendimentos totais (e últimos 90d)
- Última visita
- Próximo agendamento (ou "nenhum")
- Total gasto
- Ticket médio
- Frequência média

### Próximo atendimento (se existir)

Card grande com data/hora/serviço + atalhos **Remarcar** / **Cancelar** / **Ver**.

### Seções (tabs ou acordeão)

1. **Histórico de atendimentos** — lista cronológica de bookings (completed / cancelled / no_show), tap abre o booking.
2. **Journal** — notas, fotos, medidas, lembretes; botão **+ Anotação** (leva a `journal-entry`).
3. **Pacotes** — pacotes ativos + histórico; botão **+ Adicionar pacote** (leva a `assign-package`).
4. **Financeiro** — invoices abertas, pagas, histórico; saldo devedor em destaque se > 0.
5. **Formulários** — respostas a `form-response` (se houver).
6. **Perfil** — campos editáveis: telefone, email, aniversário, endereço, tags, observação livre. Inline edit.

### Action bar fixo (bottom)

- **+ Atendimento** (abre create-appointment pré-preenchido com este cliente).
- **Mensagem** (abre WhatsApp com o telefone do cliente, ou chat com Silvia se tier Silvia).

## Aggregate API

O detail puxa de 5+ módulos. Proposta: **um endpoint agregador** no módulo client:

```
client.get_full(workspace_id, client_id) → {
  client: { ... },
  stats: { total_bookings, last_visit_at, next_booking_at, total_spent_cents, avg_ticket_cents, avg_frequency_days },
  next_booking: Booking | null,
  bookings_recent: Booking[],
  packages_active: ClientPackage[],
  invoices_open: Invoice[],
  journal_recent: JournalEntry[],
  forms_recent: FormResponse[],
  silvia_summary: string
}
```

Caches aggressively via `_internal/cache` com invalidação em eventos `booking.*`, `invoice.*`, `client_journal.appended`, `client_package.*`.

Sections com scroll deep ainda paginam via APIs dos módulos originais.

## Agent angle (Silvia)

Same tools: `client.get_full`, `client.get`, `schedule_booking.list`, `client_journal.list`, etc.

**Reactive**:
- *"Silvia, me fala da Maria"* → retorna o `silvia_summary` expandido com insights.
- *"Qual o histórico da Maria?"* → lista de atendimentos.
- *"O que a Maria pediu da última vez?"* → último journal entry relevante.

**Autonomous**:
- **Antes do atendimento** (30min antes): push *"Maria chega em 30min. Última vez pediu franja curta; hoje é manicure."*
- **Retorno devido**: quando `last_visit_at + avg_frequency_days` passa com folga, sugere follow-up.

## Modules touched

- `_workspace/client` — `get_full` (novo agregador).
- `_workspace/schedule-booking` — histórico + next.
- `_workspace/client-journal` — notas.
- `_workspace/client-package` — pacotes ativos.
- `_workspace/finance-invoice` — financeiro.
- `_workspace/form-response` — formulários.
- `_workspace/ai-insight` — `silvia_summary` com insights de frequência e pattern.
- `_internal/cache` — cache agressivo do payload.

## Doc changes

- ⚠ `_workspace/client/README.md` — adicionar método `client.get_full(workspace_id, id)` com shape acima. Documentar que é cache-backed.
- ⚠ `_workspace/ai/insight.md` — adicionar topic `client_summary` (resumo por cliente).
- ⚠ `_workspace/ai/action.md` — adicionar rule `pre_appointment_brief` (push 30min antes com contexto do cliente).

## Open questions

- **Tabs vs acordeão vs scroll contínuo** pras seções? Propondo **scroll contínuo com âncoras** (menos taps, mobile-friendly).
- **Fotos de journal**: onde mostrar? Grid separado dentro de Journal ou misturado no feed? Propondo **grid dedicado** em aba visual "Fotos" dentro de Journal.
- **Merge prompt**: quando outro cliente com mesmo telefone é detectado, banner no header ofertando merge?
- **Privacy**: cliente pode solicitar exclusão (LGPD). Doc do fluxo em caso separado.
