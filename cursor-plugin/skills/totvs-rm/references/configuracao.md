# Configuração guiada

Use este passo só quando os dois estiverem ausentes: o MCP de SQL Server com o nome `sqlserver` e um `projeto-cliente.md` preenchido na raiz do projeto. Com um dos dois presente, siga o modo correspondente em `../SKILL.md` e não reconfigure.

Não responda pergunta de RM de memória enquanto configura. Não instale pacote, não grave arquivo e não altere configuração global do agente sem confirmação explícita. Não execute SQL de escrita. Não peça que a pessoa cole a senha no chat quando houver variável de ambiente.

## 1. Detectar

1. Procure `projeto-cliente.md` na raiz do projeto. Ele está preenchido quando tem endereço do RM, nome da base e ambiente (`homologação` ou `produção`). Arquivo vazio, só com o texto do modelo, ou sem esses três campos, não conta.
2. Procure um servidor MCP cujo nome seja exatamente `sqlserver`. Confira a lista do agente, se o comando existir: `claude mcp list`, `codex mcp list`, `gemini mcp list`, `opencode mcp list`. No Cursor, a lista fica em `.cursor/mcp.json` ou `~/.cursor/mcp.json`. Se as ferramentas desse servidor não aparecerem na sessão, ele não está disponível.
3. Um MCP de SQL com outro nome não substitui `sqlserver`. Diga o nome que encontrou e pergunte se a pessoa quer um registro adicional chamado `sqlserver`. Não renomeie o que já existe por conta própria.

Se faltarem os dois, pare a resposta técnica e faça a pergunta do passo 2.

## 2. Escolher o modo

Pergunte, e espere a resposta:

> O RM será consultado de qual modo?
>
> 1. Local: acesso direto ao banco SQL Server, por um MCP chamado `sqlserver`.
> 2. Cliente: sem acesso direto ao banco. A sondagem é no RM do cliente, com autorização, e o projeto passa a ter `projeto-cliente.md`.

## 3. Modo local

### Opções de MCP

Há duas opções conferidas. Não invente outro pacote. Não use o nome npm sem escopo `mssql-mcp`: esse nome publica outro servidor, o [BYMCS/mssql-mcp](https://github.com/BYMCS/mssql-mcp), cuja ferramenta `mssql_run_sql_query` aceita SQL arbitrário, inclusive DDL e DML. O repositório [MCopin/mssql-mcp](https://github.com/MCopin/mssql-mcp) não é esse pacote. O `package.json` dele está marcado como `private` e não é o que o `npx mssql-mcp` instala.

O SQL MCP Server da Microsoft, dentro do Data API builder, é oficial e mantido pela Microsoft ([documentação](https://learn.microsoft.com/en-us/azure/data-api-builder/mcp/overview)). Ele não executa SQL livre: só entidades já cadastradas. Por isso não serve para a consulta de validação em `dbo.GDIC` e não é uma das opções abaixo.

**Opção A, preferida quando a instalação precisa ser `npx`.** [`@connorbritain/mssql-mcp-reader`](https://github.com/ConnorBritain/mssql-mcp-reader), pacote npm de mesmo nome, licença MIT. O README descreve um servidor somente leitura: a ferramenta `read_data` aceita `SELECT` e o pacote não inclui inserção, atualização, exclusão nem DDL. Autenticação documentada: `SQL_AUTH_MODE` = `sql`, `windows` ou `aad` (padrão `aad`). Variáveis documentadas: `SERVER_NAME`, `DATABASE_NAME`, `SQL_AUTH_MODE`, `SQL_USERNAME`, `SQL_PASSWORD`. O README exige usuário e senha nos modos `sql` e `windows`. Não documenta variável de porta. Se a porta não for 1433, não invente o nome da variável: diga que a porta ficou `A confirmar` e peça à pessoa o trecho da documentação do pacote que indica onde ela entra.

**Opção B, preferida quando a porta precisa ir na configuração e a trava de leitura fica no servidor.** [MCopin/mssql-mcp](https://github.com/MCopin/mssql-mcp), versão `1.1.0` no `package.json` consultado, licença não declarada nesse arquivo. Não está no npm. Instalação: clonar o repositório e rodar `node index.js` depois de `npm install` na pasta clonada. O README define somente leitura como padrão (`MSSQL_MCP_READONLY=true`): aceita um único `SELECT` ou `WITH`, rejeita escrita e executa a consulta numa transação que sofre rollback. Variáveis: `DB_CONFIG_HOST`, `DB_CONFIG_PORT` (padrão 1433), `DB_CONFIG_DATABASE`, `DB_CONFIG_USER`, `DB_CONFIG_PASSWORD`. Mantenha `MSSQL_MCP_READONLY` omitido ou `true`. Não documenta autenticação Windows nem Entra. Não afirme que aceita.

Mostre as duas, diga por que cada uma está na lista e espere a pessoa escolher. A opção A é o caminho curto. A opção B é o caminho com porta explícita.

### O que perguntar

- Servidor (host).
- Porta. Padrão 1433.
- Nome da base.
- Autenticação: login SQL, Windows ou Microsoft Entra.

Senha: não peça o valor no chat. Peça que a pessoa exporte a variável no ambiente do processo que inicia o agente e responda só que a variável está definida.

- Opção A, modos `sql` e `windows`: o nome documentado é `SQL_PASSWORD`.
- Opção A, modo `aad`: o README não exige senha. Não peça senha.
- Opção B: o nome documentado é `DB_CONFIG_PASSWORD`.

Não rode `echo` dessa variável, não leia o valor para a conversa e não grave o valor em arquivo. Se a sessão do agente não enxerga o ambiente em que a pessoa exportou a variável, peça para iniciar o agente de novo a partir desse ambiente. Confirme a presença sem exibir o conteúdo, por exemplo `test -n "$SQL_PASSWORD" && echo definida`.

Login de banco: peça um login com leitura, de preferência limitado a `db_datareader`. Não use `sa`.

### Registrar com o nome `sqlserver`

Mostre o texto exato do arquivo e o comando de instalação. Espere um sim. Prefira o arquivo de projeto. Configuração de usuário (`~/.claude.json`, `~/.cursor/mcp.json`, `~/.codex/config.toml`, `~/.gemini/settings.json`, `~/.config/opencode/opencode.json`) é global: peça uma confirmação separada antes de alterá-la.

Não use `claude mcp add --env`, `codex mcp add --env` nem `gemini mcp add -e` passando a senha. Esses comandos gravam o valor no arquivo de configuração.

Os exemplos abaixo são a opção A, modo `sql`. Troque comando, argumentos e nomes de variável se a pessoa escolheu a opção B. Onde estiver `<servidor>`, `<base>` e `<usuario>`, use só o que a pessoa informou. A senha aparece apenas como referência à variável.

Claude Code, arquivo de projeto `.mcp.json`. A expansão `${VAR}` em `env` está na [documentação do Claude Code](https://code.claude.com/docs/en/mcp-servers).

```json
{
  "mcpServers": {
    "sqlserver": {
      "command": "npx",
      "args": ["-y", "@connorbritain/mssql-mcp-reader@latest"],
      "env": {
        "SERVER_NAME": "<servidor>",
        "DATABASE_NAME": "<base>",
        "SQL_AUTH_MODE": "sql",
        "SQL_USERNAME": "<usuario>",
        "SQL_PASSWORD": "${SQL_PASSWORD}"
      }
    }
  }
}
```

Cursor, arquivo de projeto `.cursor/mcp.json`. A forma `${env:NOME}` está na [documentação de MCP do Cursor](https://cursor.com/docs/mcp). O arquivo de usuário é `~/.cursor/mcp.json`.

```json
{
  "mcpServers": {
    "sqlserver": {
      "command": "npx",
      "args": ["-y", "@connorbritain/mssql-mcp-reader@latest"],
      "env": {
        "SERVER_NAME": "<servidor>",
        "DATABASE_NAME": "<base>",
        "SQL_AUTH_MODE": "sql",
        "SQL_USERNAME": "<usuario>",
        "SQL_PASSWORD": "${env:SQL_PASSWORD}"
      }
    }
  }
}
```

Codex, arquivo de projeto `.codex/config.toml`. O arquivo de usuário é `~/.codex/config.toml`. A referência de configuração descreve `env` como valores gravados e `env_vars` como nomes herdados do ambiente do processo ([config reference](https://learn.chatgpt.com/docs/config-file/config-reference)). A senha vai só em `env_vars`.

```toml
[mcp_servers.sqlserver]
command = "npx"
args = ["-y", "@connorbritain/mssql-mcp-reader@latest"]
enabled = true
env_vars = ["SQL_PASSWORD"]

[mcp_servers.sqlserver.env]
SERVER_NAME = "<servidor>"
DATABASE_NAME = "<base>"
SQL_AUTH_MODE = "sql"
SQL_USERNAME = "<usuario>"
```

Gemini CLI, arquivo de projeto `.gemini/settings.json`. O de usuário é `~/.gemini/settings.json`. A [documentação](https://github.com/google-gemini/gemini-cli/blob/HEAD/docs/tools/mcp-server.md) manda declarar a variável em `env` com expansão `$NOME`, porque o CLI remove do ambiente herdado variáveis cujo nome contém `PASSWORD`. `gemini mcp add` sem `-e` deixa o segredo fora do arquivo. O escopo padrão desse comando é o projeto.

```json
{
  "mcpServers": {
    "sqlserver": {
      "command": "npx",
      "args": ["-y", "@connorbritain/mssql-mcp-reader@latest"],
      "env": {
        "SERVER_NAME": "<servidor>",
        "DATABASE_NAME": "<base>",
        "SQL_AUTH_MODE": "sql",
        "SQL_USERNAME": "<usuario>",
        "SQL_PASSWORD": "$SQL_PASSWORD"
      }
    }
  }
}
```

OpenCode, arquivo de projeto `opencode.json`. Na documentação atual o servidor fica em `mcp.servers` ([OpenCode v2](https://opencode.ai/v2/docs/mcp-servers/)). Em versões anteriores o nome do servidor ficava direto em `mcp`, com os mesmos campos `type`, `command` e `environment` ([documentação anterior](https://opencode.ai/docs/mcp-servers/)). Use a forma que o `opencode.json` já existente usar. A senha entra como `{env:SQL_PASSWORD}`.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "servers": {
      "sqlserver": {
        "type": "local",
        "command": ["npx", "-y", "@connorbritain/mssql-mcp-reader@latest"],
        "environment": {
          "SERVER_NAME": "<servidor>",
          "DATABASE_NAME": "<base>",
          "SQL_AUTH_MODE": "sql",
          "SQL_USERNAME": "<usuario>",
          "SQL_PASSWORD": "{env:SQL_PASSWORD}"
        }
      }
    }
  }
}
```

Opção B, no lugar de `npx`: `"command": "node"` e o argumento é o caminho do `index.js` clonado, com `DB_CONFIG_HOST`, `DB_CONFIG_PORT`, `DB_CONFIG_DATABASE`, `DB_CONFIG_USER` e a senha na variável `DB_CONFIG_PASSWORD`, pela mesma regra de cada agente. Não grave `.env` dentro do clone com a senha. O README do MCopin aceita as variáveis pelo ambiente do processo.

O arquivo de MCP não leva a senha. Se o host identificar um cliente, não o versione. Ofereça acrescentar o arquivo ao `.gitignore` e só edite o ignore depois de um sim.

### Validar

Depois de gravar, peça para recarregar os MCP ou abrir uma sessão nova se o agente não recarregar sozinho. Quando a ferramenta de leitura do servidor `sqlserver` aparecer, execute só esta consulta:

```sql
SELECT TOP 5 DB_NAME() AS BASE_CONECTADA, TABELA, DESCRICAO
FROM dbo.GDIC
WHERE COLUNA = '#' AND ISNULL(STATUS, 0) = 0
```

Na opção A a ferramenta documentada é `read_data`. Na opção B também é `read_data`. Não chame ferramenta de escrita, de procedimento armazenado nem de SQL livre que o servidor marque como mutável.

Leia `BASE_CONECTADA` no resultado e pergunte se é a base esperada. Só então o modo local está confirmado. Se a ferramenta não aparecer ou a consulta falhar, pare. Não complete a resposta de memória.

## 4. Modo cliente

Pergunte três coisas, e nada de credencial:

- Endereço do RM (URL ou host pelo qual a pessoa chega ao RM).
- Nome da base.
- Ambiente: `homologação` ou `produção`.

Mostre o `projeto-cliente.md` preenchido a partir de `projeto-cliente.template.md`, nesta pasta. Espere um sim antes de gravar na raiz do projeto. Não grave o arquivo dentro da skill. Não coloque usuário, senha, token nem connection string.

Se o ambiente for `produção`, avise que a sondagem cria sentença no RM e que o passo de autorização de `modo-cliente.md` continua obrigatório antes de qualquer `POST`. Gravar o `projeto-cliente.md` não é sondagem.

Ofereça ignorar `projeto-cliente.md` no Git e só altere o `.gitignore` com confirmação.

Com o arquivo preenchido, o modo cliente está escolhido. Camadas 2 e 3 continuam dependendo da sondagem autorizada. Não responda essas camadas de memória.
