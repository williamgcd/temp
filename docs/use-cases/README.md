# Use cases

Concrete flows that drive module decisions. Each case follows the same template (scenario, user journey, agent angle, modules touched, doc changes) so we can cross-reference without re-reading prose.

## Numbering convention

Groups are numbered two digits; each use case inside a group is a descriptive filename (no extra prefix needed).

| Group | Area |
|---|---|
| `00-onboarding/` | Primeiro acesso, smart import via Google, boas-vindas da Silvia. |
| `01-home/` | Tela inicial do app (landing after login). |
| `02-schedule/` | Agenda: criar, ver, remarcar, bloquear, configurar. |
| `03-clients/` | Ficha de cliente, histórico, pacotes, journal. |
| `04-catalog/` | Serviços, produtos, pacotes, cursos. |
| `05-finance/` | Invoice, payment, payout, commission, cashbox, tax. |
| `06-marketing/` | Campanhas, referral, loyalty, giftcard, promotion. |
| `07-forms-docs/` | Formulários, assinatura digital, anexos. |
| `08-ai-silvia/` | Silvia como produto: chat, insights, ações autônomas. |
| `09-settings/` | Configurações (cruzam quase todos módulos). |

Groups are created as use cases are written — no empty folders up front.

## Writing style

- pt-BR no conteúdo (mensagens, nomes de campos, citações da Silvia).
- Tabelas onde couber. Frases curtas. Sem seções decorativas.
- Cada use case termina com **Doc changes** listando exatamente quais arquivos de módulo precisam atualizar.
- **Open questions** no final quando há decisões pendentes.

## Index

### 02-schedule
- [create-appointment](./02-schedule/create-appointment.md) — **Create**. 3 toques; Silvia preenche o resto.
- [see-the-schedule](./02-schedule/see-the-schedule.md) — **Read**. Lista (default), Dia, Mês; Silvia empurra snapshots no tier autônomo.
- [update-booking](./02-schedule/update-booking.md) — **Update + Delete**. Reschedule, edit fields, complete, no-show, cancel (delete = cancel com motivo `deleted`).
- [encaixe](./02-schedule/encaixe.md) — Variante rápida do create: walk-in / "tem como hoje?".
- [receive-booking-request](./02-schedule/receive-booking-request.md) — Inbound. Self-serve link público + WhatsApp (tier Silvia).
- [manage-availability](./02-schedule/manage-availability.md) — Working hours, blocks, holidays.
- [configure-policies](./02-schedule/configure-policies.md) — Lembretes, janelas, fees, depósito, buffers, granularidade, duração default, sobreposição.

### 03-clients
- [see-client-list](./03-clients/see-client-list.md) — **Read** (lista). Busca, filtros (sumiram, aniversariantes, com pacote), ordenação.
- [see-client-detail](./03-clients/see-client-detail.md) — **Read** (perfil). Hub com histórico, journal, pacotes, financeiro; agregador `client.get_full`.
- [manage-client](./03-clients/manage-client.md) — **C + U + D**. Criar manual, importar (contatos / CSV), editar, arquivar, bloquear.
- [journal-entry](./03-clients/journal-entry.md) — **C** em `client-journal`. Notas, fotos, medições, lembretes; voz-pra-journal no tier Silvia.
- [merge-clients](./03-clients/merge-clients.md) — Dedupe. Auto-detect + merge review + unmerge dentro de 7 dias.
- [assign-package](./03-clients/assign-package.md) — Grant + redenção de `client-package`; integra com `finance-invoice`.
