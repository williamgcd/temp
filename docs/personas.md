# Personas

Who we're building for. Every module doc and use case should reference these by name — if a feature doesn't serve at least one, we reconsider it.

**Scope for v1**: solo / self. Multi-pro (salon with staff, franchise) is a later expansion via `workspace-relate` and `workspace-member`.

---

## Camila — atende em casa

**One-liner**: autônoma que atende clientes no quartinho/sala de casa.

- **Serviços típicos**: manicure, depilação, cabelo, sobrancelha.
- **Clientela**: ~40 clientes ativos, boca-a-boca + Instagram, WhatsApp pra marcar.
- **Agenda**: tardes e sábados; mistura horário marcado com encaixe.
- **Pagamentos**: **PIX dominante**, algum dinheiro, cartão é raro.
- **Fiscal**: MEI, às vezes informal.
- **Ferramentas atuais**: caderno + WhatsApp + Notes no celular.
- **Maior dor**: esquece horário, não sabe quanto lucrou no mês, manda lembrete na mão.
- **Quer**: parar de esquecer, ver quanto realmente ganha, cliente receber lembrete sozinho.
- **Dimensões de produto**:
  - Divide receita? Não.
  - Walk-ins? Sim, encaixe.
  - No-show custa? Médio.
  - Sofisticação esperada da UI? Baixa — tem que caber num celular, em pouco toque.

## Júlia — aluga cadeira ou sala

**One-liner**: profissional solo trabalhando em espaço de terceiros (salão, studio compartilhado).

- **Serviços típicos**: lash, barbearia, cabelo, design de sobrancelha.
- **Paga ao dono do espaço**: aluguel fixo mensal, % da receita, ou os dois.
- **Agenda**: 9–19 estruturado, limitado pelo horário do salão.
- **Pagamentos**: cartão (maquininha própria ou do salão), PIX, pouco dinheiro.
- **Fiscal**: MEI quase sempre.
- **Ferramentas atuais**: agenda do salão (papel ou sistema do dono) + WhatsApp + planilha.
- **Maior dor**: não sabe quanto sobra depois do aluguel/comissão; conciliar repasse da maquininha é confuso.
- **Quer**: ver **lucro líquido real**, não bruto; separar o que é dela do que é do salão.
- **Dimensões de produto**:
  - Divide receita? **Sim** (aluguel ou comissão).
  - Walk-ins? Depende do salão.
  - No-show custa? Médio — ela paga pelo espaço mesmo vazio.
  - Sofisticação esperada da UI? Média.

## Bianca — estúdio próprio solo

**One-liner**: profissional com estúdio próprio (sala alugada ou comprada), clientela fidelizada, ticket alto.

- **Serviços típicos**: design de sobrancelha, micropigmentação, estética, lash premium.
- **Clientela**: agenda cheia, reservas com antecedência, recorrência mensal.
- **Agenda**: horários fixos, sessões longas, depósito comum.
- **Pagamentos**: cartão **com parcelamento** + PIX; poucos walk-ins.
- **Fiscal**: MEI ou Simples Nacional.
- **Ferramentas atuais**: já tenta algum sistema (Trinks, Belle, planilha avançada).
- **Maior dor**: no-show é caro (sessão longa = tarde perdida); gestão de pacotes e retorno.
- **Quer**: reduzir no-show, automatizar follow-up ("volta em 30 dias"), entender quem é cliente top.
- **Dimensões de produto**:
  - Divide receita? Não.
  - Walk-ins? Raro.
  - No-show custa? **Alto**.
  - Sofisticação esperada da UI? Média-alta — aceita mais telas se trouxer valor.

---

## Comparação rápida

| | Camila | Júlia | Bianca |
|---|---|---|---|
| Espaço | casa | alugado/compartilhado | próprio |
| Canal de cobrança principal | PIX | Cartão | Cartão parcelado + PIX |
| Divide receita com terceiro | Não | **Sim** | Não |
| Walk-ins | Sim | Depende | Raro |
| No-show custa | Médio | Médio | **Alto** |
| Sofisticação de UI esperada | Baixa | Média | Média-alta |
| Ticket médio | Baixo | Médio | Alto |
| Volume (clientes/semana) | Médio | Médio-alto | Baixo-médio |

## Upgrade path (fora do escopo v1)

- **Rafa — contratou uma auxiliar**: Camila ou Bianca que cresceu e virou 2 pessoas. Primeiro degrau de multi-pro. Desbloqueia `workspace-member` + `finance-commission`.
- **Salão completo**: 4+ profissionais, dono administra. Desbloqueia `workspace-relate` (rede), gestão de agenda em grade.

## Prioridade de design v1

1. **Camila** — maior mercado, pior ferramenta atual, menos edge cases.
2. **Júlia** — desbloqueia `finance-commission` e reconciliação de maquininha.
3. **Bianca** — desbloqueia pacote, depósito, follow-up automatizado.

## Como usar essas personas nos docs

Em cada módulo e use case, responder:
- **Quem**: uma (ou mais) das três.
- **Como se manifesta pra essa persona**: se diferente da outra, documentar.
- **Se não serve nenhuma das três**: cortar ou justificar.
