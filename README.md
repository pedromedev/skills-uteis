# skills-uteis

Três skills para um agente levantar o contexto técnico de um documento TOTVS
e, em seguida, redigir a MIT. A URL canônica deste repositório está em
[`distribucao.json`](distribucao.json). Os comandos abaixo repetem o valor
atual. Se divergirem do JSON, vale o JSON.

| Skill | Função |
|---|---|
| `totvs-rm` | Dicionário, parametrização e SQL do TOTVS RM. Não inventa tabela, campo nem parâmetro. |
| `totvs-tdn` | Pesquisa e validação de documentação oficial no TDN da TOTVS. |
| `escrever-mits` | Entrevista o analista e redige a MIT em uma cópia do template Word. |

## Fluxo recomendado

O contexto técnico vem antes do documento.

1. `totvs-tdn` localiza e abre o TDN oficial do assunto. A resposta traz título, link, versão visível e limites. Se não houver TDN, isso fica registrado.
2. `totvs-rm` confirma tabela, campo, relacionamento e parâmetro na base em uso. Sem consulta ao vivo, o agente para. Camadas 2 e 3 não saem de memória nem de outra base.
3. O que foi confirmado vai para `contexto/ENTREVISTA-DOCUMENTO.md`. Lacuna fica exatamente como `A confirmar`.
4. `escrever-mits` lê essa pasta, exige o caminho exato do template Word, revisa cada seção com o analista e gera o `.docx` e as pendências em `saidas-mits/`.

A aprovação do documento continua com o analista. Nenhuma skill declara a MIT pronta.

### Exemplo curto

Pedido: MIT041 de diagrama do processo de matrícula no RM Educacional.

- TDN: buscar matrícula e matriz curricular no repositório TDN, abrir a página e anotar URL e versão. Release note não substitui o guia.
- RM: na base conectada, perguntar ao `dbo.GDIC` quais tabelas e colunas vivas descrevem grade e matrícula (`ISNULL(STATUS, 0) = 0`). Não assumir uma tabela "padrão" e não copiar código de coligada de outra base.
- Contexto: gravar fontes, URLs e o que ficou `A confirmar` em `contexto/ENTREVISTA-DOCUMENTO.md`.
- Documento: informar o caminho exato do `.docx` do template e deixar `escrever-mits` conduzir a revisão seção a seção.

No Cursor, com o plugin instalado, o mesmo roteiro é o comando `/fluxo-documento-totvs`. As etapas também existem sozinhas: `/totvs-rm`, `/totvs-tdn` e `/escrever-mit`.

## O que cada skill faz

### totvs-rm

Consulta o RM em dois modos, e só responde com fonte:

- **Modo local.** MCP de SQL Server disponível para o agente com o nome `sqlserver`. Antes de tratar um resultado como fato, o agente confirma a qual base o MCP está conectado.
- **Modo cliente.** Existe um `projeto-cliente.md` preenchido na raiz do projeto. O agente não tem o banco. Camadas 2 e 3 só por sondagem autorizada no RM daquele cliente. O modelo sem credencial está em `skills/totvs-rm/references/projeto-cliente.exemplo.md`.

Sem o MCP `sqlserver` e sem `projeto-cliente.md`, a skill conduz a configuração em `skills/totvs-rm/references/configuracao.md`. Ela pergunta o modo, mostra as opções de MCP e só instala ou grava arquivo depois de uma confirmação explícita. Não há senha, connection string nem token neste pacote. A senha fica em variável de ambiente, nunca em arquivo do repositório ou da skill.

Camada 1 é a estrutura do dicionário (`GDIC`, `GCAMPOS`, `GLINKSREL`, `GMODULO`), documentada na skill. Camadas 2 e 3 (o que existe nesta base, parâmetros, coligadas, códigos cadastrados) só com consulta ao vivo. SQL que altera dados, e criar, alterar ou remover sentença, exigem confirmação explícita do usuário. Criar sentença é escrita, mesmo quando o texto é um `SELECT`.

### totvs-tdn

Busca no repositório TDN da Central do Cliente, abre a página em `tdn.totvs.com` e devolve título, link e síntese curta. Não inventa procedimento, não copia trechos longos e não trata artigo de suporte ou conteúdo de terceiros como documentação oficial.

### escrever-mits

Entrevista o analista e preenche uma cópia do template Word indicado pelo caminho exato. Excel e PDF são fontes, nunca a saída. A cópia `.docx` é editada com Python, no formato nativo do Microsoft Word, sem LibreOffice. A redação é formal, sem emojis, com revisão editorial do Humanizer ou do fallback em `skills/escrever-mits/references/humanizer-editorial.md`. Tabelas novas e diagramas Mermaid pedem confirmação. A decisão de adequação é do analista.

A skill `escrever-mits` continua indo para os mesmos diretórios de instalação de antes (`.claude/skills/escrever-mits/`, `.agents/skills/escrever-mits/`, `.cursor/skills/escrever-mits/`). A origem no pacote passou da raiz para `skills/escrever-mits/`, e a cópia inclui a pasta `references/`.

## Pré-requisito do totvs-rm

A configuração é guiada. Se faltar o MCP e o `projeto-cliente.md`, o agente segue `skills/totvs-rm/references/configuracao.md`: pergunta se o modo é local ou cliente e espera confirmação antes de instalar pacote, gravar arquivo ou alterar configuração global.

1. **Modo local:** MCP de SQL Server registrado com o nome `sqlserver`. O guia cita duas opções conferidas (um pacote npm somente leitura e um servidor local somente leitura que não está no npm) e o registro em Claude Code, Cursor, Codex, Gemini CLI e OpenCode. Este repositório não distribui `mcp.json`, host, usuário nem senha. A senha fica em variável de ambiente.
2. **Modo cliente:** o agente cria `projeto-cliente.md` na raiz do projeto de trabalho, a partir de `skills/totvs-rm/references/projeto-cliente.template.md`, com endereço do RM, nome da base e ambiente. Sem credencial, fora deste pacote e fora de repositório público.

O Humanizer é opcional, para a redação de `escrever-mits`:

```sh
npx skills add blader/humanizer --global
```

Sem ele, vale o fallback da própria skill.

## Estrutura do pacote

```text
distribucao.json          URL canônica e lista das skills
AGENTS.md                 instruções para o agente, em qualquer ferramenta que leia este arquivo
skills/
  totvs-rm/               SKILL.md, references/, conhecimento/, agents/openai.yaml
  totvs-tdn/              SKILL.md, references/, agents/openai.yaml
  escrever-mits/          SKILL.md, references/humanizer-editorial.md, agents/openai.yaml
commands/                 /totvs-rm, /totvs-tdn, /escrever-mit, /fluxo-documento-totvs
cursor-rule.mdc           adaptador Cursor da MIT
cursor-rule-totvs-rm.mdc
cursor-rule-totvs-tdn.mdc
cursor-plugin/            plugin Cursor autocontido (skills, commands, rules)
references/               mesma adaptação editorial, no caminho antigo
THIRD_PARTY_NOTICES.md
```

Não há `SKILL.md` na raiz. O instalador `npx skills` trata um `SKILL.md` na raiz como pacote de skill única e não segue para as outras. As três ficam em `skills/<nome>/SKILL.md`, com `name` e `description` no frontmatter, que é o formato lido por Claude Code, Codex, Cursor, Gemini CLI e `npx skills add`.

`cursor-plugin/skills/` repete `skills/` para o plugin continuar instalável sozinho. A pasta canônica é `skills/`.

## Instalação

Um agente que receba a URL do repositório faz o seguinte, sem pedir o roteiro de novo:

1. Ler `distribucao.json` e usar `clone`, `url` e `skills`.
2. Clonar e copiar cada pasta de `skills/` inteira, com `SKILL.md`, `references/` e `conhecimento/`. Não copiar só o `SKILL.md`.
3. Escolher o diretório do agente na tabela abaixo. Se a ferramenta lê `.agents/skills`, esse caminho serve para Codex, Cursor, Gemini CLI e OpenCode ao mesmo tempo.
4. Copiar `commands/` só onde o agente tem pasta de comandos. O Codex não registra slash command: a skill entra pelo `SKILL.md`.
5. Não criar credencial. Para `totvs-rm`, verificar o MCP `sqlserver` ou avisar que falta `projeto-cliente.md`.
6. Conferir que existem `SKILL.md` e `references/` nas três pastas de destino.

Valor atual, igual ao JSON:

```text
https://github.com/pedromedev/skills-uteis
https://github.com/pedromedev/skills-uteis.git
```

### npx skills

Instala as três skills no agente detectado. `--copy` evita symlink.

```sh
npx skills add pedromedev/skills-uteis --skill totvs-rm --skill totvs-tdn --skill escrever-mits --copy -y
```

Para um agente específico, acrescente `-a` com um destes nomes: `claude-code`, `cursor`, `codex`, `gemini-cli`, `opencode`. Exemplo para o Gemini CLI, no escopo do usuário:

```sh
npx skills add pedromedev/skills-uteis --skill totvs-rm --skill totvs-tdn --skill escrever-mits -a gemini-cli --copy -g -y
```

Lista o que o repositório publica, sem instalar:

```sh
npx skills add pedromedev/skills-uteis --list
```

### Cópia manual

```sh
git clone https://github.com/pedromedev/skills-uteis.git
cd skills-uteis
```

Troque `DEST` pelo diretório da tabela. A cópia é a pasta inteira.

```sh
mkdir -p "$DEST"
cp -R skills/totvs-rm skills/totvs-tdn skills/escrever-mits "$DEST/"
test -f "$DEST/totvs-rm/SKILL.md"
test -f "$DEST/totvs-rm/references/dicionario.md"
test -f "$DEST/totvs-tdn/SKILL.md"
test -f "$DEST/totvs-tdn/references/formula-visual-custom-activity.md"
test -f "$DEST/escrever-mits/SKILL.md"
test -f "$DEST/escrever-mits/references/humanizer-editorial.md"
```

| Agente | Projeto | Usuário |
|---|---|---|
| Claude Code | `.claude/skills` | `~/.claude/skills` |
| Codex | `.agents/skills` | `~/.agents/skills` ou `~/.codex/skills` |
| Cursor | `.agents/skills` ou `.cursor/skills` | `~/.cursor/skills` |
| Gemini CLI | `.agents/skills` ou `.gemini/skills` | `~/.gemini/skills` |
| OpenCode | `.agents/skills` ou `.opencode/skills` | conforme o escopo do OpenCode |

Claude Code também aceita `.agents/skills` em várias versões. Use um só diretório por projeto para não haver cópias divergentes.

Comandos, quando o agente tiver essa pasta:

```sh
mkdir -p .claude/commands
cp commands/totvs-rm.md commands/totvs-tdn.md commands/escrever-mit.md commands/fluxo-documento-totvs.md .claude/commands/
```

Para o usuário, troque `.claude/commands` por `~/.claude/commands`. O OpenCode usa `.opencode/commands`.

### Cursor, além da pasta de skills

Regras opcionais, em `.cursor/rules/*.mdc`:

```sh
mkdir -p .cursor/rules
cp cursor-rule.mdc .cursor/rules/escrever-mits.mdc
cp cursor-rule-totvs-rm.mdc .cursor/rules/totvs-rm.mdc
cp cursor-rule-totvs-tdn.mdc .cursor/rules/totvs-tdn.mdc
```

`alwaysApply` é `false`. Cada regra só orienta a ativação da skill correspondente.

O plugin em `cursor-plugin/` traz as três skills, os quatro comandos e as regras. O manifesto está em `cursor-plugin/.cursor-plugin/plugin.json`, com nome `skills-uteis` e a URL do repositório. Instale esse diretório pelo fluxo de plugins do Cursor. Os comandos do plugin são `/totvs-rm`, `/totvs-tdn`, `/escrever-mit` e `/fluxo-documento-totvs`.

### Codex

Skills em `.agents/skills/<nome>/SKILL.md`, com o frontmatter preservado. `AGENTS.md` complementa a skill e não a substitui. Se o projeto já tiver `AGENTS.md`, mescle à mão. Só sobrescreva quando não houver instrução a preservar:

```sh
cp AGENTS.md ./AGENTS.md
```

O Codex não registra `/escrever-mit`, `/totvs-rm` nem `/totvs-tdn`. A ativação é pelo contexto e pelo `SKILL.md`. O arquivo `agents/openai.yaml` dentro de cada skill é só rótulo de interface.

### Gemini CLI

A CLI descobre skills em `.gemini/skills/` e no alias `.agents/skills/`. Além da cópia manual e do `npx skills`, há o instalador dela, uma skill por vez:

```sh
gemini skills install https://github.com/pedromedev/skills-uteis.git --path skills/totvs-rm --consent
gemini skills install https://github.com/pedromedev/skills-uteis.git --path skills/totvs-tdn --consent
gemini skills install https://github.com/pedromedev/skills-uteis.git --path skills/escrever-mits --consent
```

`--scope workspace` limita a instalação ao projeto. Neste repositório, `GEMINI.md` aponta para `AGENTS.md`.

### Quem só lê AGENTS.md

Abra `AGENTS.md` e siga a seção de instalação. Ela usa a mesma URL de `distribucao.json`.
