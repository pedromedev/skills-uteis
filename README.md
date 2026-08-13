# escrever-mits-universal

Skill reutilizável para entrevistar um analista e preparar MITs TOTVS usando
fontes de projeto e um template Word. O fluxo lê `contexto/` (ou outra pasta
indicada), explora links pertinentes no navegador, exige o caminho exato do
template Word, prepara um rascunho, revisa
cada seção na ordem com o analista, resolve conflitos, pede autorização final e
gera Word em `saidas-mits/`, acompanhado de pendências Markdown.

A cópia `.docx` é editada localmente com Python, preservando o formato nativo do
Microsoft Word, sem LibreOffice ou conversões intermediárias. A redação é formal,
sem emojis, e passa por revisão editorial obrigatória inspirada no Humanizer,
sem alteração de fatos. Consulte `references/humanizer-editorial.md` e
`THIRD_PARTY_NOTICES.md` para a adaptação, atribuição e licença.

A skill pode reutilizar ou criar tabelas Word quando agregarem clareza e gerar
fluxos Mermaid como SVG estático, usando a CLI oficial local `mmdc` e PNG de
fallback quando necessário. Tabelas novas e diagramas exigem confirmação do
analista; a validação visual é manual no Word. Fontes `.mmd`, SVG e PNG
intermediários ficam fora de `saidas-mits/`.

A skill não aprova nem valida automaticamente a MIT. A decisão de adequação,
revisão e aprovação final é do analista.

## Estrutura do pacote

- `SKILL.md` — pacote-fonte independente, com frontmatter YAML e o fluxo
  completo, regras de segurança, rastreabilidade e limites de edição.
- `AGENTS.md` — instruções curtas para instalações em Codex.
- `cursor-rule.mdc` — adaptador opcional para as regras do Cursor; não substitui
  nem duplica `SKILL.md`.
- `commands/escrever-mit.md` — comando `/escrever-mit` para harnesses que
  suportam comandos personalizados.
- `cursor-plugin/` — plugin instalável do Cursor com manifesto, comando e
  skill.
- `references/humanizer-editorial.md` — fallback editorial local em português,
  usado quando a skill opcional `humanizer` não estiver instalada.
- `THIRD_PARTY_NOTICES.md` — atribuição e licença da referência Humanizer.

O catálogo de templates dentro de `SKILL.md` é apenas uma referência do
repositório de origem. Em outro projeto, os caminhos podem ser diferentes.
Nenhum binário de template faz parte deste pacote.

## Fluxo resumido

1. Ler `contexto/` por padrão ou a pasta alternativa informada.
2. Ler Word, Excel e PDF como fontes e solicitar prints na conversa para
   arquivos ilegíveis ou protegidos.
3. Explorar as URLs e os links internos pertinentes, registrando as páginas
   consultadas e respeitando controles de acesso.
4. Confirmar o caminho exato de um template Word; Excel e PDF nunca são a
   saída.
5. Inspecionar o template sem alterá-lo e preparar o rascunho.
6. Apresentar as seções na ordem, perguntando `OK ou correção?`; reescrever até
   cada seção ser confirmada.
7. Resolver conflitos com o analista, fazer a revisão completa e pedir
   autorização final.
8. Antes de apresentar cada seção, ativar a skill opcional `humanizer` ou
   aplicar o fallback editorial local; repetir a revisão, com segunda auditoria,
   antes da revisão completa e antes da gravação, sem emojis e sem alterar fatos.
9. Editar a cópia Word com Python e gerar `CODIGOMIT-PROJETO-DATA.docx` e o Markdown de pendências em
   `saidas-mits/`; acrescentar `-v2`, `-v3` etc. em caso de colisão.

## Instalação

### Dependência editorial opcional

O Humanizer é opcional e funciona como skill/prompt editorial, não como motor
factual. Para instalar a skill oficial no escopo global, use:

```sh
npx skills add blader/humanizer --global
```

Se ela não estiver instalada, aplique o fallback embutido em
`references/humanizer-editorial.md`. Em ambos os casos, preserve fatos,
identificadores, códigos, números, restrições, critérios e decisões confirmadas;
não envie conteúdo do cliente a serviços externos sem autorização.

Revise o conteúdo antes de instalar. Os comandos abaixo são exemplos de cópia
executados a partir da raiz do repositório; ajuste os caminhos ao seu ambiente.
Não há URL de repositório presumida. Se o pacote vier de outro local, obtenha-o
por download ou use `git clone <URL-DO-REPOSITORIO>` e depois copie os arquivos.

### Claude Code

Instalação no projeto:

```sh
mkdir -p .claude/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md .claude/skills/escrever-mits/SKILL.md
mkdir -p .claude/commands
cp -R skills/escrever-mits-universal/commands/escrever-mit.md .claude/commands/escrever-mit.md
```

Instalação para o usuário:

```sh
mkdir -p ~/.claude/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md ~/.claude/skills/escrever-mits/SKILL.md
mkdir -p ~/.claude/commands
cp -R skills/escrever-mits-universal/commands/escrever-mit.md ~/.claude/commands/escrever-mit.md
```

O caminho de projeto é `.claude/skills/escrever-mits/SKILL.md`; o caminho de
usuário é `~/.claude/skills/...`.

### OpenCode

Instalação no projeto:

```sh
mkdir -p .opencode/skills/escrever-mits
cp -R skills/escrever-mits-universal/SKILL.md .opencode/skills/escrever-mits/SKILL.md
mkdir -p .opencode/commands
cp -R skills/escrever-mits-universal/commands/escrever-mit.md .opencode/commands/escrever-mit.md
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

O Codex não oferece um mecanismo oficial para registrar slash commands
personalizados. Portanto, a skill é ativada pelo contexto e pelo
`.agents/skills/escrever-mits/SKILL.md`, não por `/escrever-mit`.

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

Para ter `/escrever-mit` no Cursor, distribua `commands/escrever-mit.md` como
comando de um plugin do Cursor. A skill e a regra de projeto continuam sendo
as instalações locais recomendadas; `.cursor/rules/*.mdc` não cria sozinha um
slash command. Este pacote inclui um plugin pronto em `cursor-plugin/`, que
pode ser instalado no Cursor conforme o fluxo de plugins do seu ambiente.
