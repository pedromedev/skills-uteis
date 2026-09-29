---
name: totvs-rm
description: >-
  Use quando o pedido envolver tabela, campo ou relacionamento do TOTVS RM, SQL
  para o RM, módulo, sentença SQL, ou termos GDIC, GCAMPOS, GLINKSREL, GMODULO,
  GlbConsSqlData, coligada, RM ou TOTVS.
---
# TOTVS RM

**Nunca invente nome de tabela, campo, DataServer ou parâmetro do RM.**
Sem confirmação numa fonte, não escreve no código nem afirma na resposta.

**Violar a letra das regras é violar o espírito das regras.**

Os caminhos deste arquivo são relativos à pasta desta skill (o diretório que
contém este `SKILL.md`), não à raiz do projeto do usuário.

## Antes de qualquer resposta: em que modo você está?

Existe um `projeto-cliente.md` preenchido na raiz do projeto atual?

- **Sim → modo cliente.** Você não tem o banco. Camadas 2 e 3 só por sondagem no RM do cliente, com autorização — leia `references/modo-cliente.md`.
- **Não → modo local.** Você tem o MCP de SQL Server configurado (esperado: `sqlserver`). Antes de tratar qualquer resultado como autoritativo, confirme a qual base ele está conectado.

Sem MCP e sem `projeto-cliente.md`? Pare e peça configuração. Não responda de memória — nenhum dos dois modos permite isso.

Não há credencial neste pacote. A conexão do MCP `sqlserver` e o arquivo
`projeto-cliente.md` ficam na máquina de quem usa. Não grave senha, connection
string nem token na skill, na resposta ou no repositório.

## O que pode vir daqui e o que exige consulta

| Camada | Exemplo | Fonte |
|---|---|---|
| 1. Estrutura do dicionário | colunas de GDIC, GCAMPOS, GLINKSREL, GMODULO | `references/dicionario.md` |
| 2. Conteúdo do dicionário | quais tabelas e campos existem nesta base | **consulta ao vivo, sempre** |
| 3. Parametrização | valores de parâmetro, coligadas, códigos cadastrados | **consulta ao vivo, sempre** |
| 4. Semântica do produto | o que a rotina faz, domínio de um campo | `conhecimento/` |

Camadas 2 e 3 variam por cliente. **Nunca respondê-las com dado de outra base.**

## Fluxo

1. Descobrir a tabela no dicionário — `dbo.GDIC`, sempre qualificado, sempre limitado (`references/sql-no-rm.md`).
2. Confirmar campos e relacionamentos antes de escrever.
3. Escrever o SQL seguindo `references/sql-no-rm.md`.
4. Validar com amostra limitada antes de entregar como definitivo.
5. Erro de API: o código HTTP é o envelope — leia o corpo antes de concluir a causa.

## Red flags — pare e confirme

- "É a tabela padrão do RM" / "normalmente é PFUNC"
- "A estrutura é a mesma, então os campos do cliente são esses"
- "O forte candidato é X" / "provavelmente é Y"
- "Confirmo depois" / "é rápido, ajusto se der errado"
- "Subo a sentença e aviso depois"

**Ressalva não é licença.** Marcar um nome como "a confirmar" e usá-lo mesmo assim é falha. A incerteza deve **bloquear a entrega**.

## Registro de conhecimento

Descobriu semântica nova? Registre em `conhecimento/`, com nível de validação, evidência e data. Não registre dado de cliente, nome de cliente, arquivo interno nem credencial.

## Referências (pasta da skill)

`references/dicionario.md` · `references/modulos.md` · `references/sql-no-rm.md` · `references/parametrizacao.md` · `references/modo-cliente.md` · `references/deploy-sentenca.md` · `references/pesquisa-semantica.md`

## Limites com o usuário

Não execute SQL que altere dados nem ações em produção sem confirmação explícita do usuário. Criar, alterar ou remover sentença no RM é escrita, mesmo quando o texto da sentença é um `SELECT`. Diferencie fato confirmado, hipótese e recomendação.
