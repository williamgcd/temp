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
- [create-appointment](./02-schedule/create-appointment.md) — Sabriza marca um atendimento em 3 toques; Silvia preenche o resto.
