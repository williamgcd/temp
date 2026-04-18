# 03-clients / Merge clients

**Personas**: Sabriza (comum porque cria cliente pelo nome; dupes acontecem).

**D**edupe: duas (ou mais) entradas que são a mesma pessoa, consolidar numa só mantendo todo o histórico.

## Scenarios

- Sabriza marcou "Maria" e "Maria Silva" em momentos diferentes; percebe que são a mesma pessoa.
- Self-serve link criou "Ana Paula" e Sabriza já tinha "Ana P." no sistema.
- Silvia detecta: mesmo telefone em dois clients.

## User journey (UI)

### Triggered by user

Entrada: na lista de clientes, **menu overflow > Mesclar clientes**.

Fluxo:
1. **Selecionar clientes a mesclar** (checkbox no item da lista). Min 2, max 5.
2. Toca **Continuar** → abre **Merge review** screen.
3. **Master selection**: qual cliente é o "principal" (destino da mescla). Default = o que tem mais dados (Silvia sugere).
4. **Field-by-field merge**: para cada campo (nome, telefone, email, aniversário, etc.) onde há conflito, Sabriza escolhe o valor a manter. Se só um cliente tem o campo, mantém.
5. **Preview**: card final mostrando como o cliente master vai ficar.
6. **Confirmar merge**.

Efeito:
- Master cliente mantém `id` e recebe todos os dados consolidados.
- Outros clients são marcados `merged_into = master_id` e `status = archived`.
- Todos os `schedule_booking`, `finance_invoice`, `client_journal`, `client_package` dos merged são **reassignados** ao master via `client.merge`.
- Audit entry com snapshot das alterações.

### Triggered by Silvia

Silvia detecta: mesmo telefone ou nome fuzzy match alto → banner no client detail:

> *"Essa cliente pode ser a mesma que [Maria Silva]. Quer que eu mescle os históricos?"*

Tap **Sim** abre Merge review direto com os dois pré-selecionados.

## Undo

Merge gera um `merge_token` válido por 7 dias. Dentro da janela:
- Banner no master cliente: *"Mesclado há X dias — Desfazer"*.
- Desfazer restaura os merged como `active` e reattacha referências originais.

Depois de 7 dias, merge é permanente (referencial).

## Agent angle (Silvia)

Same tools: `client.merge`, `client.unmerge` (se dentro da janela).

**Reactive**:
- *"Silvia, mescla a Maria Silva com a Maria S."* → abre merge review (nunca executa direto sem confirmação).
- *"Tem clientes duplicadas?"* → lista candidatos por similaridade.

**Autonomous** (detection only, never auto-exec):
- **Same phone**: dois clients com mesmo telefone verificado → sugere merge no feed da Silvia.
- **Fuzzy name + same timeslot pattern**: "Maria" e "Maria Silva" ambos com atendimentos no mesmo dia da semana → baixa confiança, sugere com prudência.
- **Email match**.

## Modules touched

- `_workspace/client` — **novo** `client.merge({ master_id, from_ids[], field_choices })`, `client.unmerge(merge_token)`, `client.find_duplicates(workspace_id)`.
- `_workspace/schedule-booking`, `_workspace/finance-invoice`, `_workspace/client-journal`, `_workspace/client-package`, `_workspace/form-response` — atualização de `client_id` nas referências.
- `_internal/audit` — snapshot do merge.
- `_workspace/ai-insight` — topic `duplicate_clients_suggestion`.
- `_workspace/ai-action` — rule `suggest_merge_on_duplicate` (sugere, nunca executa).

## Doc changes

- ⚠ `_workspace/client/README.md` — adicionar:
  - `client.merge({ master_id, from_ids, field_choices })` — retorna `merge_token`.
  - `client.unmerge(merge_token)` — só funciona dentro de 7 dias.
  - `client.find_duplicates(workspace_id)` — retorna pares candidatos.
  - Campo `merged_into?` no data model (nullable fk pro master).
- ⚠ Nota: módulos que referenciam `client_id` precisam **aceitar reassignment via evento `client.merged`** ou chamada explícita do merge. Documentar idempotência.

## Open questions

- **Cascade on finance-invoice**: invoices pagos — reassign é seguro? Propondo sim, com audit; refund/chargeback continuam vinculados ao master.
- **Fuzzy matching threshold**: quão agressivo? Propondo score > 0.85 pra sugestão, > 0.95 pra auto-banner.
- **Campo `merged_into` visível na UI ou oculto?** Oculto por default; aparece na aba "Arquivadas" se alguém procurar.
- **Merge de 3+ clients ao mesmo tempo**: suportar no v1 ou só pair merge? Propondo pair merge v1; batch v1.1.
