# Deploy de sentença — `GlbConsSqlData`

Contrato do canal REST, validado ponta a ponta em 2026-07-29 contra um RM
real. Seções marcadas **herdado, não verificado nesta validação** vêm de
material anterior e não foram retestadas — confirme antes de depender delas.

## Autenticação e contexto

```
Authorization: Basic base64(usuario:senha)
Content-Type:  application/json
CODCOLIGADA:   <n>
CODFILIAL:     <n>
```

## Chave primária

`CODCOLIGADA$_$APLICACAO$_$CODSENTENCA`, percent-encoded na URL quando
usada em rota (`0$_$G$_$DIAG.DIC.01` → `0%24_%24G%24_%24DIAG.DIC.01`).

## Criar — `POST /RMSRestDataServer/rest/GlbConsSqlData`

Corpo mínimo que funciona — `GConsSqlCampos` e `GConsSqlParams` **não são
obrigatórios** para criar:

```json
{
  "CODCOLIGADA": 0,
  "APLICACAO": "G",
  "CODSENTENCA": "DIAG.DIC.01",
  "TITULO": "Diagnostico - dicionario por tabela",
  "SENTENCA": "SELECT dicionario.TABELA, dicionario.COLUNA FROM dbo.GDIC dicionario WHERE dicionario.TABELA = :NOMETABELA",
  "DISPONIVELFILTRO": 0,
  "DISPONIVELRELATORIO": 0,
  "DISPONIVELVISAO": 0
}
```

Campos obrigatórios em `dbo.GCONSSQL` (`sys.columns`, `is_nullable = 0`):

| Coluna | Tipo | Tamanho |
|---|---|---|
| CODCOLIGADA | DCODCOLIGADA | — |
| APLICACAO | char | 1 |
| **CODSENTENCA** | varchar | **16** |
| DISPONIVELFILTRO / DISPONIVELRELATORIO / DISPONIVELVISAO | DLOGICO | tem `DEFAULT (1)`, mas envie explícito |

### Armadilha 1 — `CODSENTENCA` cabe 16 caracteres

Estourar dá `HTTP 500` com corpo:

```json
{"messages":[{"code":"0007","type":"error",
  "detail":"Failed to enable constraints. One or more rows contain values
            violating non-null, unique, or foreign-key constraints."}],
 "length":0,"data":null}
```

A mensagem **não menciona qual campo**. `DIAG.DICIONARIO.01` (18
caracteres) falha; `DIAG.DIC.01` (11) funciona. Confira o tamanho antes de
montar `CODSENTENCA`, não depois do erro.

### Armadilha 2 — o corpo do erro sempre tem a causa, inclusive nos 500

O `Content-Length` do 500 acima é 197, não zero. Ler o stream de resposta
pela API errada (por exemplo `Exception.Response.GetResponseStream()` sem
posicionar em `0`, no `.NET`/PowerShell) pode devolver string vazia por
engano — o que **não** significa que o servidor não mandou nada. Use o
mecanismo que lê o corpo do erro corretamente na sua stack antes de
concluir "corpo vazio".

**Nunca conclua causa a partir do código de status sozinho.** Este canal
devolve 500 tanto para payload inválido quanto para DataServer inexistente
— o código não distingue os casos, a mensagem sim.

## Executar — `GET /api/framework/v1/consultaSQLServer/RealizaConsulta/{cod}/{coligada}/{aplicacao}`

```
/api/framework/v1/consultaSQLServer/RealizaConsulta/DIAG.DIC.01/0/G?parameters=NOMETABELA=PFUNC
```

**O terceiro segmento é a `APLICACAO`**, não uma flag fixa — precisa bater
com o valor usado na criação. Múltiplos parâmetros: separar com `;` em
`parameters`.

Devolve **array JSON puro**, sem envelope — diferente do
`RMSRestDataServer`, que envelopa em `{messages, length, data}`.

### Armadilha 3 — 404 na execução é ambíguo

Pode ser rota inexistente ou sentença inexistente. O corpo distingue:

```json
{"code":"FE011","message":"Não foi possível encontrar a consulta SQL utilizando a seguinte chave: 0|G|DIAG.DIC.01.",...}
```

`code: "FE011"` é **sentença não encontrada** — a rota existe, o endpoint
está publicado. Um 404 genérico sem esse `code` sugere rota errada.

## Ler a coleção — `GET /RMSRestDataServer/rest/GlbConsSqlData`

Devolve **todas as sentenças cadastradas**, com o texto SQL de cada uma —
829 sentenças, 2 MB, nesta validação. **Use com filtro sempre que possível**
(busca por chave primária); trate a listagem completa como último recurso.

### Armadilha 4 — a listagem não traz os filhos

`GConsSqlCampos` e `GConsSqlParams` vêm **vazios** no `GET` da coleção.
Só o `GET` por chave primária (`GET
/RMSRestDataServer/rest/GlbConsSqlData/{pk}`) os preenche. Contar campos
pela listagem leva à conclusão errada de que a sentença não tem nenhum.

## Remover — `DELETE /RMSRestDataServer/rest/GlbConsSqlData/{pk}`

Devolve `200` com o registro removido. **Remove os filhos junto** —
verificado: zero registros órfãos em `GCONSSQLCAMPOS` e
`GCONSSQLPARAMETROS` após a remoção. Confirmação obrigatória: repita a
execução (passo anterior) e espere `404`/`FE011` — ver `modo-cliente.md`.

## `GConsSqlCampos` — uma entrada por coluna do `SELECT`

```json
{
  "CODCOLIGADA": 0, "APLICACAO": "G", "CODSENTENCA": "<PREFIXO>.<CODIGO>",
  "COLUNA": "TABELA", "DESCRICAO": "TABELA"
}
```

## `GConsSqlParams` — uma entrada por `:NOME`

```json
{
  "CODCOLIGADA": 0, "APLICACAO": "G", "CODSENTENCA": "<PREFIXO>.<CODIGO>",
  "NOME": "NOMETABELA", "TIPO": "System.String", "DESCRICAO": "Nome da tabela física"
}
```

`TIPO` **não é obrigatório** — confirmado em `dbo.GCONSSQLPARAMETROS`: 95
de 296 parâmetros reais têm `TIPO` nulo. Colunas reais dessa tabela:
`APLICACAO`, `CODCOLIGADA`, `CODSENTENCA`, `DESCRICAO`, `NOME`, `TIPO` — não
existe coluna `TAMANHO`, apesar de aparecer em versões antigas de exemplo.

### Mapa de tipos SQL → .NET, confirmado no banco

Distribuição real em `GCONSSQLPARAMETROS.TIPO` (296 linhas):

| Tipo .NET | Ocorrências |
|---|---|
| *(nulo)* | 95 |
| `System.String` | 87 |
| `System.DateTime` | 51 |
| `System.Int32` | 43 |
| `System.Int64` | 9 |
| `System.Int16` | 7 |
| `System.Decimal` | 4 |

## Ciclo GET antes de PUT ao editar — **herdado, não verificado nesta validação**

A skill anterior descreve que o DataServer valida `CONTROLE`, `VERSAO` e
`GUID` ao editar, exigindo `GET` do registro atual antes do `PUT` para
reenviar esses campos com os valores vigentes (controle de concorrência
otimista). **A validação cobriu criar, executar, remover e confirmar
remoção. Não cobriu edição via `PUT`.** Trate como hipótese até confirmar
contra o RM real.
