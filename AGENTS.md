# Instruções para o agente

Este pacote publica três skills. A URL canônica está em `distribucao.json`
(`url`: https://github.com/pedromedev/skills-uteis). Se este arquivo divergir
do JSON, vale o JSON. O procedimento completo de instalação está no `README.md`.

| Skill | Pasta neste pacote | Quando usar |
|---|---|---|
| `totvs-rm` | `skills/totvs-rm/SKILL.md` | Tabela, campo, relacionamento, módulo, parâmetro ou SQL do TOTVS RM |
| `totvs-tdn` | `skills/totvs-tdn/SKILL.md` | Documentação oficial no TDN da TOTVS |
| `escrever-mits` | `skills/escrever-mits/SKILL.md` | Redigir a MIT em Word, seção a seção |

Depois de instaladas, as mesmas pastas ficam em `.agents/skills/<nome>/`, ou no
diretório equivalente do agente (`.claude/skills/`, `.cursor/skills/`,
`.gemini/skills/`). Leia o `SKILL.md` da skill e os arquivos relativos à pasta
dela. Não procure caminho absoluto de máquina.

## Instalação

1. Ler `distribucao.json` e clonar `clone` (https://github.com/pedromedev/skills-uteis.git).
2. Copiar as três pastas de `skills/` inteiras para o diretório de skills do agente. `references/` e `conhecimento/` vão junto.
3. Copiar `commands/` só se o agente tiver pasta de comandos. Codex não tem slash command oficial.
4. Não gravar credencial. `totvs-rm` em modo local usa o MCP `sqlserver`. Em modo cliente, o projeto precisa de `projeto-cliente.md` sem senha. Se faltarem os dois, a configuração é guiada: siga `skills/totvs-rm/references/configuracao.md` e peça confirmação antes de instalar ou alterar config.

Atalho, quando `npx` estiver disponível:

```sh
npx skills add pedromedev/skills-uteis --skill totvs-rm --skill totvs-tdn --skill escrever-mits --copy -y
```

Destinos manuais: Claude Code em `.claude/skills` ou `~/.claude/skills`; Codex em `.agents/skills`; Cursor em `.agents/skills` ou `.cursor/skills`; Gemini CLI em `.agents/skills` ou `.gemini/skills`. Confira `SKILL.md` e `references/` nas três pastas. Se o projeto já tiver `AGENTS.md`, mescle estas seções. Não o sobrescreva.

## Fluxo recomendado

1. `totvs-tdn` para achar e abrir o TDN oficial. Registrar título, link, versão visível e limites. Dizer quando não houver TDN.
2. `totvs-rm` para confirmar tabela, campo, relacionamento e parâmetro na base em uso. Sem MCP `sqlserver` e sem `projeto-cliente.md`, conduzir a configuração guiada em `references/configuracao.md` da skill, com confirmação antes de instalar qualquer coisa.
3. Gravar o confirmado em `contexto/ENTREVISTA-DOCUMENTO.md`. Lacuna = `A confirmar`.
4. `escrever-mits` com essa pasta e o caminho exato do template Word. Saída em `saidas-mits/`, só depois da autorização do analista.

Exemplo: MIT041 do processo de matrícula no RM Educacional. O TDN é aberto e citado. O dicionário é consultado na base conectada, com `dbo.GDIC` qualificado e resultado limitado. Nada é copiado de outra base. O contexto recebe as fontes e os `A confirmar`. A MIT só então entra em revisão seção a seção.

## totvs-rm

Nunca invente nome de tabela, campo, DataServer ou parâmetro do RM. Sem confirmação numa fonte, não escreva no código nem afirme na resposta.

Camadas 2 e 3 (conteúdo do dicionário e parametrização) só com consulta ao vivo na base em uso. Nunca respondê-las com dado de outra base. Camada 1 está em `references/dicionario.md`, relativa à skill. Semântica de produto vai para `conhecimento/`, com nível, evidência e data, sem dado de cliente.

Não execute SQL que altere dados nem ações em produção sem confirmação explícita do usuário. Criar, alterar ou remover sentença é escrita, mesmo quando o texto é um `SELECT`. Não instale pacote nem altere configuração global do agente sem a mesma confirmação. Diferencie fato confirmado, hipótese e recomendação. Ressalva não autoriza uso: "a confirmar" bloqueia a entrega.

Não há credencial no pacote. Não grave senha, connection string nem token. A senha do modo local fica em variável de ambiente. Se faltarem o MCP `sqlserver` e o `projeto-cliente.md`, leia `references/configuracao.md` na pasta da skill, pergunte o modo e só então proponha a instalação.

## totvs-tdn

Pesquise o repositório TDN, abra a página em `tdn.totvs.com` e entregue título, link e síntese curta. Preserve alertas, pré-requisitos e limites. Não invente procedimento, não reproduza trechos longos e não trate artigo de suporte ou conteúdo de terceiros como documentação oficial. Para atividade customizada de Fórmula Visual, leia `references/formula-visual-custom-activity.md` na pasta da skill.

## escrever-mits

Use `contexto/` por padrão, exija o caminho exato do template Word, trate Excel e PDF somente como fontes e revise cada seção com o analista antes de pedir autorização final.

Nunca altere o template original, invente dados ou declare aprovação automática. Gere somente a cópia Word e o relatório de pendências em `saidas-mits/`, marque lacunas como `A confirmar` e preserve a rastreabilidade das fontes, conflitos e correções. A decisão final de adequação é do analista.

Explore no navegador os links fornecidos e seus links internos pertinentes, registrando as URLs consultadas e sem contornar controles de acesso. Edite a cópia `.docx` localmente com Python, preserve o formato nativo do Microsoft Word e não use LibreOffice ou conversões intermediárias. Use linguagem formal, não use emojis e aplique a revisão editorial obrigatória do Humanizer ou o fallback em `references/humanizer-editorial.md` ao lado do `SKILL.md` (neste pacote: `skills/escrever-mits/references/humanizer-editorial.md`) antes de cada seção e novamente antes da revisão completa, com segunda auditoria, sem inventar ou alterar fatos.

Ao escrever Open XML, nunca copie formatação de título para o corpo. Clone um parágrafo corporal equivalente do template, preserve tamanho, cor, negrito e paginação, inspecione `rPr`/`pPr` e exija validação visual manual no Word.

Use tabelas Word somente quando melhorarem a comparação e prefira reutilizar as existentes. Sugira Mermaid apenas para fluxos claros e confirmados pelo analista; use `mmdc` oficial local, SVG estático e PNG de fallback quando necessário. Faça a validação visual manual no Word e mantenha fontes `.mmd` e imagens intermediárias fora de `saidas-mits/`. Registre tabelas ou diagramas criados ou alterados no relatório e não insira novos elementos sem autorização.

O Codex não possui slash commands personalizados oficiais. Use cada skill pelo contexto do projeto. Não presuma que `/escrever-mit`, `/totvs-rm`, `/totvs-tdn` ou `/fluxo-documento-totvs` serão reconhecidos fora de um harness que tenha os arquivos de `commands/` instalados.
