# Módulos do RM e códigos de APLICACOES

> Verificado em base de referência — RM versão `12.1.2510.0` — 2026-07-29.

## Onde vive o mapa

A skill herdada listava um mapa de códigos (`A` = Chronus, `P` = Labore...)
como se fosse conhecimento externo. **Não é.** O mapa existe no banco, em
`dbo.GSISTEMA`, e é **camada 2** — consulta ao vivo, não conhecimento
decorado:

```sql
SELECT CODSISTEMA, CODSISTCOMERCIAL, NOMESISTEMA, DESCRICAO
FROM dbo.GSISTEMA ORDER BY CODSISTEMA
```

`CODSISTEMA` é o código de uma letra que aparece na coluna `APLICACOES` do
`GDIC`. Confirmado nesta base, para referência de leitura — **reconfirme na
base em uso**, este mapa pode mudar entre versões e instalações:

| Código | Nome | Descrição |
|---|---|---|
| 0 | RM Custos | TOTVS Gestão de Custos |
| A | RM Chronus | TOTVS Automação de Ponto |
| B | RM Testis | TOTVS Avaliação e Pesquisa |
| C | RM Saldus | TOTVS Gestão Contábil |
| D | RM Liber | TOTVS Gestão Fiscal |
| E | RM Classis - E | Ensino Básico |
| F | RM Fluxus | TOTVS Gestão Financeira |
| G | RM Bis | TOTVS Inteligência de Negócios |
| H | RM Agilis | TOTVS Aprovações e Atendimento |
| I | RM Bonum | TOTVS Gestão Patrimonial |
| K | RM Factor | TOTVS Planejamento e Controle da Produção |
| L | RM Biblios | TOTVS Gestão Bibliotecária |
| M | RM Solum | TOTVS Construção e Projetos |
| N | RM Officina | TOTVS Manutenção |
| O | RM Saude/Janus | TOTVS Saúde Hospitais e Clínicas |
| P | RM Labore | TOTVS Folha de Pagamento |
| R | RM SSO | TOTVS Segurança e Saúde Ocupacional |
| S | RM Classis Net | TOTVS Educacional |
| T | RM Nucleus | TOTVS Gestão de Estoque, Compras e Faturamento |
| U | RM Classis - U | Ensino Superior |
| V | RM Vitae | TOTVS Gestão de Pessoas |
| W | RM Portal | TOTVS Gestão de Conteúdos |
| X | RM SGI | TOTVS Incorporação |
| Y | RM Acesso | TOTVS Controle de Acesso |

## Armadilhas confirmadas

1. **`GSISTEMA` não cobre todos os códigos em uso em `APLICACOES`.** Códigos
   observados sem entrada em `GSISTEMA`: `J` (PEP — Prontuário Eletrônico do
   Paciente), `Q` (cubos/BI — `QCUBOX`, `QCATEGORIACUBO`), `Z` (TOTVS Audit —
   `ZAUDITCONFIG`, `ZAUDITITEMS`; mesmo produto do schema `TOTVSAUDIT`). Não
   achar um código em `GSISTEMA` **não** significa que ele é inválido —
   apenas que não é um módulo licenciável nesta listagem.

2. **`GM` em `APLICACOES` é defeito de dado, não código.** Seis tabelas do
   Officina (`OFMAOOBRAPORMODELO`, `OFMAOOBRAPORSUBMODELO`,
   `OFMOTIVOAGREGACAOOBJFILHO`, `OFMOVPROCESSAMOS`, `OFPARAMETROS`,
   `OFPECAPENDENTE`) trazem `APLICACOES = 'N;GM;'`, que é `N;G;M;` com um
   separador `;` faltando. Uma busca por `LIKE '%M%'` pega essas seis por
   engano.

3. **`GMODULO` não é o mapa de códigos, e discorda de `GSISTEMA`.**
   `GMODULO` é a tabela do menu — várias linhas podem compartilhar um
   `CODSISTEMA`, e a legenda diverge. Exemplo observado: SSO aparece sob
   `V` em `GMODULO` ("Segurança e Saúde Ocupacional") e sob `R` em
   `GSISTEMA`. `GMODULO` também registra módulo desativado (`ACTIVE = 0`).
   **Não use `GMODULO` para resolver código de `APLICACOES`.**

4. **Prefixo de tabela não é garantia de módulo.** `OFPARAMETROS` tem
   prefixo `OF` mas módulo `N`; `PFUNC`, `PCCUSTO` e `PSECAO` compartilham
   prefixo `P` e módulo `P`, mas a coincidência é convenção, não regra.
   **Confirmar sempre por `APLICACOES`**, nunca pelo nome da tabela.

## Distribuição real dos códigos (contagem de tabelas por código, nesta base)

Ordem decrescente, para calibrar o que é módulo grande e o que é raro:
`G` (8.399, framework/BI), `O` (1.889), `S` (1.403), `V` (1.301), `X`
(1.175), `T` (964), `P` (958), `M` (900) — os demais abaixo de 750. Um
código raro (`J`: 61, `Q`: 251, `Z`: 252) não é sinal de erro, apenas de
módulo menos usado.
