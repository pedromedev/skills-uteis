# Instruções para Codex

Ao trabalhar com MITs TOTVS, consulte o `SKILL.md` local desta skill (na
instalação Codex: `.agents/skills/escrever-mits/SKILL.md`). Use `contexto/` por
padrão, exija o caminho exato do template Word, trate Excel/PDF somente como
fontes e revise cada seção com o analista antes de pedir autorização final.

Nunca altere o template original, invente dados ou declare aprovação automática.
Gere somente a cópia Word e o relatório de pendências em `saidas-mits/`, marque
lacunas como `A confirmar` e preserve a rastreabilidade das fontes, conflitos e
correções. A decisão final de adequação é do analista.

Explore no navegador os links fornecidos e seus links internos pertinentes,
registrando as URLs consultadas e sem contornar controles de acesso. Edite a
cópia `.docx` localmente com Python, preserve o formato nativo do Microsoft Word
e não use LibreOffice ou conversões intermediárias. Use linguagem formal, não
use emojis e aplique a revisão editorial obrigatória do Humanizer ou o fallback
em `references/humanizer-editorial.md` antes de cada seção e novamente antes da
revisão completa, com segunda auditoria, sem inventar ou alterar fatos.

Ao escrever Open XML, nunca copie formatação de título para o corpo. Clone um
parágrafo corporal equivalente do template, preserve tamanho, cor, negrito e
paginação, inspecione `rPr`/`pPr` e exija validação visual manual no Word.

Use tabelas Word somente quando melhorarem a comparação e prefira reutilizar as
existentes. Sugira Mermaid apenas para fluxos claros e confirmados pelo analista;
use `mmdc` oficial local, SVG estático e PNG de fallback quando necessário. Faça
a validação visual manual no Word e mantenha fontes `.mmd` e imagens
intermediárias fora de `saidas-mits/`. Registre tabelas ou diagramas
criados/alterados no relatório e não insira novos elementos sem autorização.

O Codex não possui slash commands personalizados oficiais; use a skill pelo
contexto do projeto, não presuma que `/escrever-mit` será reconhecido.
