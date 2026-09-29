# Regras de escrita de SQL no RM

Sete regras, cada uma com o motivo e um exemplo bom e um ruim.

## 1. Todo `SELECT` sai limitado — por filtro ou por `TOP`

**A regra herdada dizia "nunca use `TOP` no dicionário". Está errada e foi
corrigida.** O erro a evitar é resultado ilimitado, não o `TOP` em si.

No dicionário, o limite natural é o filtro: `WHERE TABELA = 'X'` já devolve
poucas dezenas de linhas, e deve vir **completo**, sem `TOP`, para não
perder campo. Sem filtro por tabela — buscando por termo, por exemplo —
`TOP` é obrigatório: `dbo.GDIC` sozinho tem 135.596 linhas.

Em dados reais (não-dicionário), `TOP` é sempre obrigatório: a maioria das
tabelas do RM é grande, e não há um filtro natural equivalente ao `TABELA =`.

```sql
-- Bom: filtro faz o limite, dicionário completo por tabela
SELECT COLUNA, DESCRICAO FROM dbo.GDIC WHERE TABELA = 'PFUNC' AND COLUNA <> '#'

-- Bom: sem filtro por tabela, TOP obrigatório
SELECT TOP 30 TABELA, DESCRICAO FROM dbo.GDIC WHERE COLUNA = '#' AND DESCRICAO LIKE '%parametro%'

-- Ruim: nem filtro nem TOP — varre 135.596 linhas
SELECT TABELA, COLUNA, DESCRICAO FROM dbo.GDIC
```

## 2. Qualificar `dbo.` no dicionário

Existe `TOTVSAUDIT.GDIC`, homônimo de auditoria com ~2.321 linhas contra as
~135.596 de `dbo.GDIC`. Sem qualificar, a consulta resolve pelo schema
padrão do usuário e pode acertar a errada — **sem erro nenhum, só resultado
incompleto**.

```sql
-- Bom
SELECT COLUNA, DESCRICAO FROM dbo.GDIC WHERE TABELA = 'PFUNC'

-- Ruim: pode devolver 2.321 linhas de auditoria em vez de 135.596 do dicionário real
SELECT COLUNA, DESCRICAO FROM GDIC WHERE TABELA = 'PFUNC'
```

## 3. Nada de `DECLARE`, `SET @var`, `IF`, `EXEC` ou T-SQL procedural

O motor de sentença do RM (`GlbConsSqlData`) não executa esse tipo de
comando — só SQL puro, com parâmetros no formato `:NOME`. Uma sentença que
usa T-SQL procedural falha ao ser criada ou executada, e o erro devolvido
não aponta a causa (ver `references/deploy-sentenca.md`).

```sql
-- Bom
SELECT funcionario.CHAPA, funcionario.NOME
FROM dbo.PFUNC funcionario
WHERE funcionario.CODCOLIGADA = :CODCOLIGADA AND funcionario.CODSITUACAO = :CODSITUACAO

-- Ruim: motor de sentença não executa
DECLARE @sit VARCHAR(1) = 'A';
SELECT CHAPA, NOME FROM PFUNC WHERE CODSITUACAO = @sit
```

## 4. `CODCOLIGADA` no `WHERE` sempre que a tabela tiver o campo

Via parâmetro `:CODCOLIGADA`. Sem isso, a consulta retorna dado de todas as
coligadas cadastradas na base — em produção de cliente, isso mistura
empresas diferentes no mesmo resultado.

```sql
-- Bom
SELECT funcionario.CHAPA, funcionario.NOME
FROM dbo.PFUNC funcionario
WHERE funcionario.CODCOLIGADA = :CODCOLIGADA

-- Ruim: mistura todas as coligadas da base
SELECT CHAPA, NOME FROM dbo.PFUNC
```

## 5. Aliases descritivos, mínimo três letras

`func`, não `f`. Em SQL que mistura tabelas de nomes parecidos (`PFUNC`,
`PFUNCAO`, `PFUNCAOCONF`), alias de uma letra é ambíguo de ler e fácil de
trocar sem querer.

```sql
-- Bom
SELECT func.CHAPA, func.NOME, cargo.DESCRICAO
FROM dbo.PFUNC func
JOIN dbo.PFUNCAO cargo ON cargo.CODCOLIGADA = func.CODCOLIGADA AND cargo.CODIGO = func.CODFUNCAO

-- Ruim: f e c não dizem nada, e f colide visualmente com FU de PFUNCAO
SELECT f.CHAPA, f.NOME, c.DESCRICAO
FROM dbo.PFUNC f
JOIN dbo.PFUNCAO c ON c.CODCOLIGADA = f.CODCOLIGADA AND c.CODIGO = f.CODFUNCAO
```

## 6. Collation: confirme, não assuma

Concatenar coluna de catálogo (`sys.columns`, `sys.objects`) com literal ou
com dado de usuário **pode** quebrar por conflito de collation — mas só
quando a instalação de fato mistura collations. **Não é o caso na base de
referência**: catálogo e dados usam `SQL_Latin1_General_CP1_CI_AI`,
e a concatenação foi testada e funciona (2026-07-29).

A regra prática, então, não é evitar concatenação — é **descobrir a
collation antes de escrever qualquer coisa que dependa dela**:

```sql
-- Primeiro: qual e a collation desta base?
SELECT CONVERT(varchar(100), DATABASEPROPERTYEX(DB_NAME(), 'Collation')) AS COLLATION_BASE

-- Concatenacao simples: funciona quando a collation e uniforme
SELECT o.name + '.' + c.name AS TABELA_COLUNA
FROM sys.columns c JOIN sys.objects o ON o.object_id = c.object_id

-- Se a base tiver collation mista e a concatenacao acima falhar, force:
SELECT o.name + '.' + c.name COLLATE DATABASE_DEFAULT AS TABELA_COLUNA
FROM sys.columns c JOIN sys.objects o ON o.object_id = c.object_id
```

O sufixo da collation também muda o comportamento de filtro: `_CI_` ignora
caixa, `_AI_` ignora acento. Nesta base os dois são insensíveis, então
`WHERE CODIGO LIKE 'Nivel%'` acha `Nível...`. Numa base `_AS_`, não acharia.

## 7. CTE (`WITH`) para queries complexas

Mais legível dentro do editor de sentenças do RM, que não tem realce de
sintaxe nem formatação automática. Uma CTE nomeada documenta a intenção de
cada etapa; uma subquery aninhada, não.

```sql
-- Bom
WITH ativos AS (
  SELECT CHAPA, CODCOLIGADA FROM dbo.PFUNC WHERE CODCOLIGADA = :CODCOLIGADA AND CODSITUACAO = 'A'
)
SELECT ativos.CHAPA, evento.DESCRICAO
FROM ativos
JOIN dbo.PEVENTO evento ON evento.CODCOLIGADA = ativos.CODCOLIGADA

-- Ruim: mesma lógica, ilegível sem realce de sintaxe
SELECT a.CHAPA, e.DESCRICAO FROM (SELECT CHAPA, CODCOLIGADA FROM dbo.PFUNC WHERE CODCOLIGADA = :CODCOLIGADA AND CODSITUACAO = 'A') a JOIN dbo.PEVENTO e ON e.CODCOLIGADA = a.CODCOLIGADA
```
