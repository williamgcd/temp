# 03-clients / Manage client (create, update, archive, block)

**Personas**: Sabriza (primary).

Cobre **C + U + D** do perfil do cliente. Criação implícita (ao marcar atendimento) está em [create-appointment](../02-schedule/create-appointment.md); aqui é criação/edit explícita.

## Scenarios

- **Criar** manualmente uma cliente nova antes de qualquer atendimento (ex: importando lista antiga do caderno).
- **Importar** da lista de contatos do telefone (iOS/Android) ou CSV.
- **Editar** telefone da Maria (ela mudou).
- **Arquivar** uma cliente que mudou de cidade (sai da lista default, mas preserva histórico).
- **Bloquear** cliente problemática (não aparece mais em buscas, Silvia não aceita booking dela).

## User journey (UI)

### Create

Entrada: **FAB +** na lista de clientes.

Bottom sheet:
- **Nome** (obrigatório).
- **WhatsApp / telefone** (opcional; se preenchido, format BR auto).
- **Email** (opcional).
- **Aniversário** (opcional).
- **Observação** (textarea opcional).
- **Tags** (chips, livre).
- Expandível **"Mais detalhes"**: endereço, pronomes, organização-mãe (pra kind=individual com parent), kind (individual/organização).
- **Salvar** → cria; toast "Cliente criada — Ver perfil".

Com WhatsApp preenchido, Silvia pergunta no chat: *"Quer que eu mande uma mensagem de boas-vindas?"* (tier Silvia auto-draft, Pro só sugere).

### Import

Entrada: **lista de clientes (empty state)** ou **menu overflow > Importar**.

Fluxo:
1. Escolhe fonte: **Contatos do telefone** (iOS/Android contact picker OS-nativo) ou **Arquivo CSV**.
2. **Contatos do telefone**: seleciona múltiplos → preview com flags de dedupe (Silvia marca quais já existem) → confirma.
3. **CSV**: upload → mapping UI (coluna X → nome, coluna Y → telefone) com detecção automática → preview → confirma.
4. Post-import: *"Importadas 23 clientes. 3 já existiam (não foram duplicadas)."*

### Update

Entrada: **Editar** no header do client detail, ou inline em qualquer campo editável.

Mesmo bottom sheet do create; **Salvar** atualiza. `client.updated` event emitido.

### Archive

Entrada: **menu overflow > Arquivar** no detail.

- Sheet de confirmação: *"Maria vai sair da lista principal mas o histórico continua. Você pode restaurar depois."*
- Confirma → `status = archived` → some da lista default (reaparece só com filtro "Arquivadas").

### Block

Entrada: **menu overflow > Bloquear**.

- Sheet com campo opcional **motivo**.
- Confirma → `status = blocked` → Silvia recusa booking requests deste cliente automaticamente; aparece apenas com filtro "Bloqueadas".

## Agent angle (Silvia)

Same tools: `client.create`, `client.update`, `client.archive`, `client.block`.

**Reactive**:
- *"Adiciona a Maria Silva, whatsapp 11999998888"* → cria.
- *"Muda o telefone da Ana pra 11988887777"* → update.
- *"Arquiva a Julia"* → archive com confirmação.

**Autonomous**:
- **Unknown number reachout**: tier Silvia recebe WhatsApp de número não cadastrado → *"Esse número escreveu pela primeira vez. É cliente nova? Quer que eu salve?"*
- **Data enrichment**: após X atendimentos sem email, *"Quer adicionar o email da Maria pra eu mandar orçamentos?"*
- **Block suggestion**: cliente com 3+ no-shows consecutivos → *"Quer bloquear a Ana? Ela deu 3 no-shows seguidos."* (nunca auto-executa).

## Modules touched

- `_workspace/client` — `create`, `update`, `archive`, `block`, `find_or_create_by_name` (usado no import pra dedupe).
- `_workspace/ai-action` — rules `unknown_number_reachout`, `data_enrichment_prompt`, `block_suggestion`.
- `_internal/audit` — log completo.

## Doc changes

- ⚠ `_workspace/client/README.md` — explicitar que:
  - `archive` é soft (status muda, dados ficam), restauração via `update(id, { status: 'active' })`.
  - `block` impede o client de aparecer em buscas públicas (`schedule-public-link.request_booking` rejeita).
  - `delete` real só via `_internal/trash` com `client.soft_delete` (LGPD / request do próprio cliente) — documentar em caso separado.
- ⚠ `_workspace/client/README.md` — adicionar `client.import_from_contacts(entries[])` / `client.import_from_csv(rows[])` como helpers que chamam `find_or_create_by_name` em batch.
- ⚠ `_workspace/ai/action.md` — rules acima.

## Open questions

- **Kind = organização**: interface colapsa no v1 (quase ninguém usa)? Propondo sim; mostrar só se usuário escolher manualmente.
- **LGPD / direito ao esquecimento**: fluxo de exclusão real precisa existir. Use case dedicado em `09-settings/` ou `03-clients/`? Propondo `09-settings/data-privacy.md`.
- **CSV mapping inteligente**: Silvia propõe mapping com LLM (lê primeiros N rows)? Provavelmente sim, ficha pra v1.
