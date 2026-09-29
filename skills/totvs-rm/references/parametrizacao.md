# Parametrização do RM

> Verificado em base de referência — RM versão `12.1.2510.0` — 2026-07-29.

**Regra central: parâmetro é camada 3.** Valor lido numa base não vale para
outra. Nunca versionar valor de parâmetro de cliente — nem em
`conhecimento/`, nem em resposta que trate o valor como fato geral do RM.

## Não existe "a tabela de parâmetros" do RM

São **219 tabelas** com `DESCRICAO LIKE '%parametro%'` no dicionário, cada
módulo com a sua, seguindo o padrão `<PREFIXO>PARAM*`. Um único módulo pode
ter várias, com escopos diferentes — `APARAM` (sistema), `APARFUN` (por
funcionário), `APARCOL` (coletivo), `APARAMUSUARIO` (por usuário). Escolher
a tabela errada dá um valor real que responde outra pergunta.

## Três formatos, escopos diferentes

| Tabela exemplo | Formato | Escopo | Chave |
|---|---|---|---|
| `dbo.GPARAMS` | larga, singleton | **global à base** | 1 linha, 1 parâmetro por coluna |
| `dbo.GPARAMETROSSISTEMA` | chave/valor | **global à base** | `ID` / `CODIGO` |
| `dbo.APARAM` (e `<PREFIXO>PARAM` de módulo) | larga | **por coligada** | `CODCOLIGADA` |

### Forma larga singleton — `dbo.GPARAMS`

Uma linha só, um parâmetro por coluna (81 colunas nesta base). Sem
`CODCOLIGADA` — vale para a base inteira. Descobrir quais parâmetros
existem é consultar o dicionário, não a tabela:

```sql
SELECT COLUNA, DESCRICAO FROM dbo.GDIC
WHERE TABELA = 'GPARAMS' AND COLUNA <> '#' AND ISNULL(STATUS, 0) = 0
ORDER BY COLUNA
```

> ### ⚠️ `dbo.GPARAMS` contém credenciais em texto
>
> Segundo o próprio dicionário: `EMERGENCYKEY` (Senha de Emergência),
> `STARTPSW` (Chave de Inicialização), `LDAPADMINPASSWORD`, `FLUIGSENHA`,
> `FLUIGCONSUMERSECRET`, `WSPASSWORDFORFLUIG`, `LICENCESERVERADDRESS`.
>
> **Nunca `SELECT * FROM dbo.GPARAMS`.** Sempre listar as colunas
> explicitamente, e só as necessárias para a pergunta feita.

### Forma chave/valor — `dbo.GPARAMETROSSISTEMA`

Colunas: `ID`, `CODIGO` (rótulo em português, não um código curto), `VALOR`,
`TIPO` (controle de tela), `FONTE` (FK para `GPARAMETROSSISTEMAFONTE`),
`VISIBILIDADE`. Sem `CODCOLIGADA` — global à base. 96 linhas nesta base.

```sql
SELECT parametro.CODIGO, parametro.VALOR
FROM dbo.GPARAMETROSSISTEMA parametro
WHERE parametro.CODIGO = :CODIGOPARAMETRO
```

`CODIGO` é texto livre em português, não um código curto — o valor do
`ID = 1` é literalmente `Nível Log Comunicação`. Filtrar por igualdade
exata é frágil: qualquer diferença de grafia, espaço ou pontuação zera o
resultado. Prefira `LIKE` com um trecho estável.

Nesta base a collation é `SQL_Latin1_General_CP1_CI_AI` — *case* e *accent
insensitive* —, então acento e caixa **não** atrapalham (`'Nivel Log%'`
acha). Confirme na base em uso antes de contar com isso; em collation
`_AS_` o acento passa a importar.

> ⚠️ Esta tabela também guarda segredo: `ID = 5` nesta base é "Chave de
> Criptografia Simétrica". Mesma regra — nunca ler `VALOR` sem necessidade
> pontual, nunca listar tudo.

### Forma larga por coligada — `dbo.APARAM` e módulos análogos

Mesmo padrão da forma singleton, mas com `CODCOLIGADA`. Uma linha por
coligada, um parâmetro por coluna (128 colunas em `APARAM` nesta base).
Sem filtrar `CODCOLIGADA`, numa base com várias coligadas, o resultado
mistura parâmetro de empresas diferentes.

```sql
SELECT parametros.CODCOLIGADA, parametros.<COLUNA_CONFIRMADA>
FROM dbo.APARAM parametros
WHERE parametros.CODCOLIGADA = :CODCOLIGADA
```

## Consulta-modelo para ler um parâmetro vigente

Três passos — o primeiro nunca é adivinhar o nome da tabela.

**1. Achar a tabela do módulo, pelo dicionário e por `APLICACOES`:**

```sql
SELECT TOP 20 TABELA, DESCRICAO FROM dbo.GDIC
WHERE COLUNA = '#' AND ISNULL(STATUS, 0) = 0
  AND APLICACOES LIKE :PADRAOMODULO AND DESCRICAO LIKE '%ar_metro%'
ORDER BY TABELA
```

**2. Descobrir qual coluna é o parâmetro procurado:**

```sql
SELECT COLUNA, DESCRICAO FROM dbo.GDIC
WHERE TABELA = :NOMETABELA AND COLUNA <> '#' AND ISNULL(STATUS, 0) = 0
ORDER BY COLUNA
```

**3. Ler, com colunas explícitas e filtro de coligada quando existir** — ver
os três formatos acima.

## Escopo não confirmado

`APARFUN` (por funcionário), `APARCOL` (coletivo) e `APARAMUSUARIO` (por
usuário) foram identificadas pelo nome e descrição, não abertas. Descubra o
escopo pelas colunas-chave antes de assumir — não generalize a partir do
padrão de `APARAM`.

## Em modo cliente

Sem MCP na base do cliente, o mesmo roteiro de três passos vale via
sentença de sondagem no canal REST — ver `modo-cliente.md`. O aviso de
segurança sobre `GPARAMS` vale com força redobrada: uma sentença de
sondagem que faça `SELECT *` nessa tabela deixaria credencial do cliente
no resultado retornado pela API, visível a quem executar a sentença.
