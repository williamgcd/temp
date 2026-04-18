# 03-clients / Assign package

**Personas**: especialmente Bianca e Júlia (pacote é diferencial de negócio); também Sabriza (vende "combo 5 manicures" com desconto).

Cobre **C + R** de `client-package` e **redenção** no booking.

## Scenarios

- **Compra**: cliente paga R$ 200 por pacote de 5 manicures (R$ 40 cada, economia de 20%).
- **Presente**: Sabriza dá 1 sessão de brinde pra cliente recorrente.
- **Manual/ajuste**: correção — cliente tinha 3 sessões no caderno antes do app.
- **Redenção**: Maria chega pra manicure → Sabriza marca que é pelo pacote (3 → 2 restantes).

## User journey (UI)

### Granting a package (C)

Entrada:

1. **Detail do cliente > seção Pacotes > + Adicionar pacote**.
2. Ou **durante o checkout de um invoice**: *"Ela quer comprar um pacote"*.

Bottom sheet:
- **Pacote** (dropdown sobre `catalog-package` com `visibility = public`).
- **Origem** (chips): `Compra`, `Presente`, `Manual`.
- **Data de aquisição** (default hoje).
- **Expira em** (default = vem do `catalog-package.expires_after_days`; editável).
- Se origem = Compra: toggle *"Gerar invoice agora"* (default on) → cria `finance-invoice` com o pacote como line item.
- **Salvar**.

### Viewing packages (R)

No detail do cliente, seção Pacotes:

- Cards para cada pacote:
  - Nome do pacote.
  - Contadores por componente: `4/5 manicures`, `1/1 pedicure`.
  - Status: `ativo` / `expirado` / `consumido`.
  - Data de aquisição, data de expiração.
  - Histórico de redenções colapsável.
- Botão **+ Adicionar pacote**.
- Histórico de pacotes terminados (colapsado).

### Redenção no booking

No detail de um booking da cliente que tem pacote ativo cobrindo aquele serviço:

- Banner discreto: *"Maria tem pacote ativo (4 manicures restantes). Usar pacote nesse atendimento?"* `[Sim] [Não]`.
- Confirmando: ao `complete`, o booking é marcado `redeemed_package_id`; o pacote decrementa. Invoice gerada (se houver) fica R$ 0 pro item coberto.

Silvia auto-propõe o "Sim" no tier Silvia se é o único pacote aplicável.

## Agent angle (Silvia)

Same tools: `client_package.grant`, `client_package.redeem`, `client_package.remaining`.

**Reactive**:
- *"Silvia, a Maria tá usando qual pacote?"* → lista ativos.
- *"Quantas sessões faltam pra Ana?"* → remaining por componente.
- *"Dá um pacote de 1 manicure de brinde pra Julia"* → grant com origem `gift`.

**Autonomous**:
- **Auto-redeem suggestion**: no `booking.completed`, se cliente tem exato um pacote aplicável, Silvia propõe redenção (ou auto-aplica se `ai.auto_redeem_package` está on).
- **Expiration warning**: 15 dias antes de expirar pacote com saldo, *"O pacote da Maria expira em 15 dias com 2 sessões. Quer avisar pra marcar?"*
- **Upsell opportunity**: cliente consumiu o último slot de pacote → *"Maria terminou o pacote. Quer oferecer um novo?"*

## Modules touched

- `_workspace/client-package` — `grant`, `redeem`, `remaining`, `list_for_client`, `expire_due`.
- `_workspace/catalog-package` — source do template.
- `_workspace/schedule-booking` — redenção vinculada ao booking via `redeemed_package_id`.
- `_workspace/finance-invoice` — criação automática na compra; line item zerada quando coberto.
- `_workspace/ai-action` — rules `auto_redeem_suggestion`, `package_expiration_warning`, `package_upsell`.

## Doc changes

- ⚠ `_workspace/client/package.md` — confirmar/adicionar:
  - `list_for_client(workspace_id, client_id, status?)`
  - `expire_due()` — job periódico pra marcar expirados.
  - Evento `client_package.expiring_soon` (15d antes).
  - Campo `redeemed_package_id?` deve existir em `schedule_booking` ou em uma relação — revisar onde vive a relação (booking → redemption).
- ⚠ `_workspace/schedule/booking.md` — adicionar campo opcional `redeemed_package_id` no data model ou referência via `client_package_redemption`.
- ⚠ `_workspace/ai/action.md` — adicionar rules acima.

## Open questions

- **Compõem vários pacotes no mesmo booking** (ex: combo que cobre manicure + pedicure)? Propondo suporte v1, decremento parcial do pacote.
- **Refund de pacote**: cliente desiste, devolve dinheiro → pacote vira `voided`, invoice refund flow. Integrado com `finance-payment.refund`.
- **Transferência de pacote entre clientes** (ex: minha cliente deu uma sessão pra irmã dela): raro, ficha v1.1?
- **Presente de pacote entre clientes**: um cliente compra, outro usa. Precisa `purchased_by_client_id` e `issued_to_client_id` (já tá em `mkt-giftcard` por exemplo). Aqui: propondo `purchased_by_client_id?` opcional no `client_package`.
