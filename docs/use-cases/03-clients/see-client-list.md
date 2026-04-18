# 03-clients / See client list

**Personas**: Sabriza (primary).

**R** on clients: a lista completa, buscável, filtrável.

## Scenario

Sabriza quer:
- Ver todas as clientes.
- Buscar a "Maria" que esqueceu o sobrenome.
- Filtrar "quem não vem há mais de 60 dias".
- Ver quem tem aniversário essa semana.

## User journey (UI)

Entrada: **tab "Clientes"** na bottom nav (ou equivalente).

Layout:

1. **Search bar** no topo, sticky. Busca por nome, telefone, observação (fuzzy, case/accent-insensitive).
2. **Filtros rápidos** como chips horizontais scrolláveis:
   - `Todas` (default)
   - `Ativas` (com atendimento nos últimos 90 dias)
   - `Sumiram` (sem atendimento há > 60 dias)
   - `Aniversariantes do mês`
   - `Com pacote ativo`
   - `Com pendência financeira`
   - `+ Novo filtro` (abre builder avançado)
3. **Ordenação** (menu): `Última visita (recente)` (default), `A-Z`, `Mais frequente`, `Maior ticket médio`.
4. **Lista**: um item por cliente.
   - Linha 1 (bold): nome.
   - Linha 2 (caption): `última visita há X dias` • telefone/whatsapp icon se existe.
   - Avatar / iniciais colorida.
   - Badges: `pacote`, `aniversário` (se próximo), `pendência` (se deve).
5. **Header sticky de letra** quando ordenação A-Z (índice lateral opcional).
6. **FAB +** → criar cliente manual.

Interações:

- **Tap** no item → detalhe da cliente (`see-client-detail`).
- **Long-press** → ações rápidas: mandar WhatsApp, copiar telefone, arquivar, bloquear.
- **Pull-to-refresh**.
- **Scroll infinito** (página de 50).

Empty states:

- Workspace novo, nenhuma cliente: Silvia cartoon + *"Suas clientes vão aparecer aqui conforme você marcar atendimentos. Quer importar seus contatos?"* + botões **Importar contatos** / **Adicionar cliente**.
- Filtro sem resultado: *"Nenhuma cliente encontrada com esses filtros."* + link "Limpar filtros".

## Agent angle (Silvia)

Same tools: `client.list({ filter, sort, search })`, `client.get`.

**Reactive**:
- *"Silvia, quem não vem há 60 dias?"* → aplica filtro e responde lista compacta com botão "Abrir em Clientes".
- *"Quantas clientes tenho?"* → contagem total e ativas.
- *"Cadê a Maria que faz cabelo?"* → busca fuzzy + top matches.

**Autonomous**:
- **Aniversário do mês**: no dia 1, *"Você tem 8 clientes aniversariando esse mês. Quer que eu mande parabéns automaticamente?"*
- **Churn alert**: mensal, *"12 clientes ativas sumiram nos últimos 60 dias. Quer que eu mande uma mensagem de reativação?"*

## Modules touched

- `_workspace/client` — `list`, `get` com filtros.
- `_workspace/schedule-booking` — para `última_visita` (aggregate).
- `_workspace/finance-invoice` — para `pendência` flag.
- `_workspace/client-package` — para `pacote ativo` flag.
- `_workspace/ai-insight` — geração de listas sugeridas.
- `_workspace/ai-action` — `birthday_batch`, `churn_alert`.

## Doc changes

- ⚠ `_workspace/client/README.md` — adicionar método `client.list({ workspace_id, search?, filter?, sort?, page?, page_size? })` e enumerar filtros canônicos: `active`, `dormant_days_gte`, `has_active_package`, `has_open_invoice`, `birthday_month`, `birthday_week`.
- ⚠ `_workspace/client/README.md` — adicionar campo derivado `last_visit_at` no payload de `list` e `get` (computado via `schedule_booking` → cacheado).

## Open questions

- Filtro "custom" abre um builder (like Notion filter) ou é fora do v1? Propondo **fora do v1**; v1 tem só os chips pré-definidos.
- Índice alfabético lateral (scrubber) vale o esforço v1? Propondo **sim se A-Z sort** está em uso.
- Import de contatos exige permissão do OS. Onde pedir? Propondo: quando usuário toca "Importar contatos", não antes.
