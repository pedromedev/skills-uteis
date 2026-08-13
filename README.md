# escrever-mits-universal

Skill reutilizável para entrevistar um analista e preparar MITs TOTVS usando
fontes de projeto e um template Word. O fluxo lê `contexto/` (ou outra pasta
indicada), exige o caminho exato do template Word, prepara um rascunho, revisa
cada seção na ordem com o analista, resolve conflitos, pede autorização final e
gera Word em `saidas-mits/`, acompanhado de pendências Markdown.

A skill não aprova nem valida automaticamente a MIT. A decisão de adequação,
revisão e aprovação final é do analista.

## Estrutura do pacote

- `SKILL.md` — pacote-fonte independente, com frontmatter YAML e o fluxo
  completo, regras de segurança, rastreabilidade e limites de edição.
- `AGENTS.md` — instruções curtas para instalações em Codex.
- `cursor-rule.mdc` — adaptador opcional para as regras do Cursor; não substitui
  nem duplica `SKILL.md`.

O catálogo de templates dentro de `SKILL.md` é apenas uma referência do
repositório de origem. Em outro projeto, os caminhos podem ser diferentes.
Nenhum binário de template faz parte deste pacote.

## Fluxo resumido

1. Ler `contexto/` por padrão ou a pasta alternativa informada.
2. Ler Word, Excel e PDF como fontes e solicitar prints na conversa para
   arquivos ilegíveis ou protegidos.
3. Confirmar o caminho exato de um template Word; Excel e PDF nunca são a
   saída.
4. Inspecionar o template sem alterá-lo e preparar o rascunho.
5. Apresentar as seções na ordem, perguntando `OK ou correção?`; reescrever até
   cada seção ser confirmada.
6. Resolver conflitos com o analista, fazer a revisão completa e pedir
   autorização final.
7. Gerar `CODIGOMIT-PROJETO-DATA.docx` e o Markdown de pendências em
   `saidas-mits/`; acrescentar `-v2`, `-v3` etc. em caso de colisão.

## Instalação

Revise o conteúdo antes de instalar. Os comandos abaixo são exemplos de cópia
executados a partir da raiz do repositório; ajuste os caminhos ao seu ambiente.
Não há URL de repositório presumida. Se o pacote vier de outro local, obtenha-o
por download ou use `git clone <URL-DO-REPOSITORIO>` e depois copie os arquivos.

### Claude Code

Instalação no projeto:

```sh
mkdir -p .claude/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md .claude/skills/escrever-mits/SKILL.md
```

Instalação para o usuário:

```sh
mkdir -p ~/.claude/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md ~/.claude/skills/escrever-mits/SKILL.md
```

O caminho de projeto é `.claude/skills/escrever-mits/SKILL.md`; o caminho de
usuário é `~/.claude/skills/...`.

### OpenCode

Instalação no projeto:

```sh
mkdir -p .opencode/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md .opencode/skills/escrever-mits/SKILL.md
```

O OpenCode também suporta `.claude/skills` e `.agents/skills`. Por exemplo, uma
instalação alternativa no projeto é:

```sh
mkdir -p .agents/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md .agents/skills/escrever-mits/SKILL.md
```

Use uma única localização preferida por projeto para evitar cópias divergentes.

### Codex

Instalação da skill no projeto:

```sh
mkdir -p .agents/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md .agents/skills/escrever-mits/SKILL.md
```

No Codex, skills ficam em `.agents/skills/escrever-mits/SKILL.md` e devem
conservar o frontmatter. Instruções gerais persistentes do projeto usam
`AGENTS.md`; elas complementam a skill e não substituem `SKILL.md`. Revise e
mescle manualmente o `AGENTS.md` do pacote com um `AGENTS.md` já existente, em
vez de sobrescrevê-lo:

```sh
cp -R skills/escrever-mits-universal/AGENTS.md ./AGENTS.md
```

Esse último comando só é apropriado quando não houver instruções gerais que
precisem ser preservadas.

### Cursor

Instalação da skill no projeto:

```sh
mkdir -p .cursor/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md .cursor/skills/escrever-mits/SKILL.md
```

O Cursor também suporta `.agents/skills`:

```sh
mkdir -p .agents/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md .agents/skills/escrever-mits/SKILL.md
```

O adaptador de regra é opcional e deve ficar em `.cursor/rules/*.mdc` (nunca
`.md`):

```sh
mkdir -p .cursor/rules
cp -R skills/escrever-mits-universal/cursor-rule.mdc .cursor/rules/escrever-mits.mdc
```

Como `alwaysApply` é `false`, a regra só orienta a ativação quando o trabalho
envolver MIT, TOTVS, Word ou Excel. Ela aponta para a skill em vez de duplicá-la.
