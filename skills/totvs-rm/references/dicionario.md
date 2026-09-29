# Tabelas de dicionário do RM

> Verificado em base de referência — RM versão `12.1.2510.0` (lida em
> `dbo.GSISTEMA.VERSAOMINIMA`) — 2026-07-29.
> Versão diferente no cliente? Confirme antes de usar — colunas e
> comportamento mudam entre versões (ver `modulos.md` para o caso já
> observado de `GATUALIZACAOVERSAO` vazia).

Esta é **camada 1**: estrutura, estável entre clientes desta mesma versão.
Conteúdo — quais tabelas e campos existem numa base específica — é camada 2
e nunca vem daqui. Consulte sempre ao vivo.

## Armadilhas desta estrutura

1. **`GDIC` tem homônimo.** `dbo.GDIC` (~135.596 linhas) e `TOTVSAUDIT.GDIC`
   (~2.321, tabela de auditoria) coexistem. Sem qualificar `dbo.`, a consulta
   resolve pelo schema padrão do usuário e pode acertar a de auditoria —
   **sem erro nenhum, só resultado incompleto**. Sempre `dbo.GDIC`.

2. **`GCAMPOS` é view, não tabela** — projeção pura de `dbo.GDIC`, sem
   nenhum `WHERE`:
   ```sql
   CREATE VIEW GCAMPOS AS
   SELECT TABELA, COLUNA, DESCRICAO DESCRICAO, RELATORIO RELATORIO, APLICACOES
   FROM GDIC
   ```
   Duas consequências: `GCAMPOS` **herda colunas deletadas** de `GDIC` (não
   filtra `STATUS`), e `GCAMPOS.DESCRICAO` é `varchar(120)` contra
   `varchar(150)` em `GDIC` — **trunca até 30 caracteres**. Prefira `GDIC`
   direto; `GCAMPOS` só serve quando as cinco colunas dela bastam.

3. **`GDIC.STATUS` marca coluna deletada** (`0` existe, `1` deletada) — e
   **existem linhas com `STATUS NULL`** que são colunas vivas nunca
   carimbadas (6 nesta base, todas de `XPROPOSTA`). O filtro correto é
   `ISNULL(STATUS, 0) = 0`, não `STATUS = 0` — este último descarta linhas
   vivas silenciosamente. Sem filtro nenhum, uma consulta pode devolver
   campo que **não existe mais** na tabela física.

4. **`GLINKSREL.MASTERFIELD` e `CHILDFIELD` são listas separadas por
   vírgula** para chave composta (`CODCOLIGADA,CHAPA`), não um campo único.
   Existem linhas com `MASTERTABLE` vazio (string vazia, não `NULL` — filtrar
   `MASTERTABLE <> ''`). E o mesmo par de tabelas pode aparecer várias vezes,
   com campos de ligação diferentes — não é 1:1 por par.

5. **Collation: confirme antes de assumir conflito.** Esta base usa
   `SQL_Latin1_General_CP1_CI_AI` — *case* e *accent insensitive* — e o
   catálogo (`sys.columns`, `sys.objects`) usa a mesma. Concatenar catálogo
   com literal ou com dado de usuário **funciona aqui**, testado em
   2026-07-29.

   Instalações com collation mista existem, e aí a concatenação quebra. Se
   acontecer, a saída é `COLLATE DATABASE_DEFAULT` ou devolver as colunas
   separadas. Descubra a collation real antes de decidir:

   ```sql
   SELECT CONVERT(varchar(100), DATABASEPROPERTYEX(DB_NAME(), 'Collation')) AS COLLATION_BASE
   ```

## dbo.GDIC — 27 colunas

Tabela física do dicionário. Cada linha descreve **uma coluna** de **uma
tabela** do RM; a linha com `COLUNA = '#'` descreve a tabela em si.

| Coluna | Tipo | Descrição (segundo o próprio GDIC) |
|---|---|---|
| TABELA | varchar(30) | Nome da Tabela Física |
| COLUNA | varchar(30) | Nome do Campo Físico |
| DESCRICAO | varchar(150) | Descrição do Campo Físico |
| RELATORIO | smallint | Indicativo de Inc. do Campo no Relatório |
| APLICACOES | varchar(100) | Códigos das Aplicações — lista `;`-separada, ver `modulos.md` |
| PORTUGAL | varchar(40) | Tradução para Portugal |
| RELPTG | smallint | Visível para Portugal |
| MEXICO | varchar(40) | Tradução para o México |
| RELMEX | smallint | Visível para o México |
| CONSULTATEXTO | DLOGICONULL | Consulta de Texto habilitada? |
| PODECONSULTARTEXTO | DLOGICONULL | Pode consultar texto? |
| ACTION | varchar(120) | Action associada |
| ACTIONKEYFIELD | varchar(255) | Campo chave da action de lookup |
| ACTIONSEARCHFIELD | varchar(255) | Campo de pesquisa da action de lookup |
| SECCOLNAME | varchar(120) | Coluna de segurança |
| SECCOLIGADACOLUMN | varchar(120) | Coligada da coluna de segurança |
| RECCREATEDBY / RECCREATEDON | varchar(50) / datetime | Criação do registro |
| RECMODIFIEDBY / RECMODIFIEDON | varchar(50) / datetime | Última modificação |
| **STATUS** | bit, nullable | **0 existe / 1 deletada** — ver armadilha 3 |
| APINAME | nvarchar(240) | Nome API |
| ANONIMIZAVEL / PESSOAL / SENSIVEL | bit | Classificação LGPD do campo |
| CODCLASSIFICACAO / CODCLASSIFICACAODETALHADO | smallint | Enum de classificação LGPD |

## dbo.GCAMPOS — 5 colunas (VIEW)

`TABELA`, `COLUNA`, `DESCRICAO` (truncada em 120), `RELATORIO`, `APLICACOES`.
Ver armadilha 2 antes de usar.

## dbo.GLINKSREL — 8 colunas, 30.849 linhas

Mapa de relacionamento estrutural entre tabelas — confirmado contra `PFUNC`:
663 ligações no `GLINKSREL` para essa tabela, contra 47 foreign keys
declaradas no catálogo do SQL Server. A ligação `AHORARIO.CODIGO` →
`PFUNC.CODHORARIO` bate exatamente com a FK `FKPFUNC_AHORARIO`.

A descrição no próprio dicionário — "Ligações dos Relatórios" — descreve a
origem histórica do dado, não limita o conteúdo: é mapa mais rico que as
constraints físicas, não mais pobre.

| Coluna | Descrição |
|---|---|
| MASTERTABLE | Nome da Tabela Master |
| CHILDTABLE | Nome da tabela detalhe |
| MASTERFIELD | Campo(s) da tabela master — lista `,`-separada, ver armadilha 4 |
| CHILDFIELD | Campo(s) da tabela detalhe — idem |
| RECCREATEDBY / RECCREATEDON / RECMODIFIEDBY / RECMODIFIEDON | Auditoria do registro |

## dbo.GMODULO — 11 colunas, 27 linhas

Tabela do menu/carregamento de módulo — **não** é o mapa de códigos de
`APLICACOES` (ver `modulos.md`). `column_id` pula de 3 para 5: há uma coluna
removida no histórico da estrutura.

| Coluna | Descrição |
|---|---|
| CLASSNAME | Nome da classe de gerenciamento do módulo |
| LOADORDER | Ordem de carregamento do menu |
| MODULECAPTION | Descrição do Módulo |
| ACTIVE | Ativo? (`0`/`1` — há módulo desativado nesta base) |
| IDSEGMENTO | Código do Segmento |
| CODSISTEMA | Código do módulo — `varchar(1)`, é o código usado em `APLICACOES` |
| PERMISSOES | Identificação do menu associado à action |
| RECCREATEDBY / RECCREATEDON / RECMODIFIEDBY / RECMODIFIEDON | Auditoria |

## Consultas úteis

Colunas de uma tabela, com a coluna deletada corretamente excluída:

```sql
SELECT COLUNA, DESCRICAO FROM dbo.GDIC
WHERE TABELA = :NOMETABELA AND COLUNA <> '#' AND ISNULL(STATUS, 0) = 0
ORDER BY COLUNA
```

Descrição da tabela em si:

```sql
SELECT DESCRICAO, APLICACOES FROM dbo.GDIC
WHERE TABELA = :NOMETABELA AND COLUNA = '#'
```

Buscar tabela candidata por termo, sempre com `TOP` — sem filtro por tabela,
o dicionário inteiro tem 135.596 linhas:

```sql
SELECT TOP 30 TABELA, DESCRICAO, APLICACOES FROM dbo.GDIC
WHERE COLUNA = '#' AND ISNULL(STATUS, 0) = 0 AND DESCRICAO LIKE :TERMO
ORDER BY TABELA
```

Relacionamentos de uma tabela:

```sql
SELECT MASTERTABLE, CHILDTABLE, MASTERFIELD, CHILDFIELD FROM dbo.GLINKSREL
WHERE (MASTERTABLE = :NOMETABELA OR CHILDTABLE = :NOMETABELA) AND MASTERTABLE <> ''
```
