# 03-clients / Journal entry

**Personas**: Sabriza (nota rápida "cliente pediu franja"). Alto valor pra Bianca (medição, fotos antes/depois, evolução).

**C** no `client-journal`: adicionar nota, foto, medição, lembrete numa cliente.

## Scenarios

- Depois de atender Maria, Sabriza quer deixar anotado *"Pediu franja curta da próxima vez."*
- Bianca mede sobrancelha e quer salvar medidas + foto antes.
- Depois de micropigmentação, Bianca marca *"retoque em 30 dias"* como lembrete acionável.

## User journey (UI)

### Entry points

1. **No detail do cliente** > seção Journal > **+ Anotação**.
2. **No detail de um booking completado** > **+ Anotação** (pre-vincula ao booking).
3. **Swipe em atendimento completado** (Lista) > atalho "Anotar".
4. **Silvia no chat**: *"anota que a Maria quer franja"*.

### Bottom sheet

- **Tipo** (chips no topo): `Nota` (default), `Foto`, `Medição`, `Lembrete`.
- **Corpo** (textarea markdown leve).
- **Anexos** (se tipo = Foto: uploader; senão, opcional).
- **Vincular ao atendimento**: dropdown com últimos atendimentos; se vindo do booking, já preenchido.
- **Data** (default = agora; editável).
- **Salvar**.

Variações por tipo:

- **Medição**: form com pares `label + valor` (ex: `Sobrancelha dir.: 4.2cm`). Armazena em `data_json` estruturado.
- **Lembrete**: campos adicionais `acionar em (data)`, gera um evento interno para Silvia/notificação; Silvia avisa no dia.

### Visualização

- Feed cronológico dentro da seção Journal do client detail.
- Item: tipo-icon + data + corpo + anexos thumb.
- Tap → expande / abre foto em full-screen.
- Long-press → editar / deletar (via `_internal/trash` soft-delete).

## Agent angle (Silvia)

Same tools: `client_journal.append`, `client_journal.list`, `client_journal.for_booking`.

**Reactive**:
- *"Silvia, anota que a Maria quer franja curta"* → append com `kind=note`.
- *"Me mostra o histórico de journal da Maria"* → listagem.
- *"Quando foi a última medição da Ana?"* → filtra por kind + data.

**Autonomous**:
- **Voice-to-journal** (tier Silvia): Sabriza manda áudio *"a Maria quer franja curta da próxima vez"* → Silvia transcreve + append + vincula ao último booking completed.
- **Post-atendimento prompt** (tier Silvia, configurável): ao `booking.completed`, *"Como foi a Maria? Quer anotar algo?"*
- **Retoque reminder**: lembrete tipo `Lembrete` dispara notificação no dia + sugestão "Quer marcar o retoque?"

## Modules touched

- `_workspace/client-journal` — `append`, `list`, `for_booking`, `update`, `delete` (via trash).
- `_workspace/document` — anexos (fotos).
- `_workspace/schedule-booking` — vínculo.
- `_platform/llm-vector` — journal entries são vetorizadas pra contexto em outros queries.
- `_workspace/ai-action` — `voice_to_journal`, `post_appointment_prompt`, `journal_reminder_fire`.

## Doc changes

- ⚠ `_workspace/client/journal.md` — adicionar `update(id, patch)` (texto rescrito, por exemplo) e `delete(id)` (via trash). Listar `kinds` atuais: `note`, `photo`, `measurement`, `reminder`. Confirmar que `data_json` é usado por `measurement` (schema livre) e `reminder` (carries `fire_at`).
- ⚠ `_workspace/client/journal.md` — adicionar evento `journal_reminder.due` disparado quando um entry do kind `reminder` atinge `fire_at`.
- ⚠ `_workspace/ai/action.md` — rules listadas acima.

## Open questions

- **Voice transcription**: usa Whisper-equivalente via `llm-worker`? Cost per minute. Ok no tier Silvia.
- **Photo storage**: compressão? versões? thumbnail? Decisão em `document` module, não aqui.
- **Template de medição** por modalidade (sobrancelha, cabelo, unhas)? Propondo **template opcional** — Sabriza pode criar templates salvos de labels comuns.
- **Privacy**: fotos de cliente são sensíveis (LGPD). Storage criptografado? Acesso logado em audit? Precisa decisão de segurança separada.
