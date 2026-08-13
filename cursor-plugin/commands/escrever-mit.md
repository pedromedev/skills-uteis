---
description: "Prepara uma MIT TOTVS a partir do contexto do projeto e de um template Word"
---

Ative a skill `escrever-mits` e execute seu fluxo completo.

Use `$ARGUMENTS` como contexto opcional. Ele pode conter o caminho exato do
template Word e/ou uma pasta alternativa de contexto.

Siga esta ordem: analisar `contexto/` ou a pasta indicada, ler as fontes,
explorar no navegador os links fornecidos e os links internos pertinentes,
inspecionar o template Word sem alterá-lo, produzir o rascunho, aplicar a
revisão editorial Humanizer ou o fallback local antes de apresentar cada seção,
revisar cada seção com o analista, corrigir até obter `OK`, aplicar novamente a
revisão editorial e sua segunda auditoria antes da revisão completa, pedir
autorização final e gerar a saída em `saidas-mits/`.

A saída é sempre um documento Word acompanhado de pendências Markdown. Nunca
altere o template original, não invente informações e não declare aprovação ou
validação automática.

Use tabelas Word somente quando agregarem clareza, preferindo as existentes, e
pergunte antes de criar novas. Para fluxos claros e confirmados, sugira Mermaid,
renderize com a CLI oficial local `mmdc` em SVG estático e mantenha PNG de fallback
temporário quando necessário. Registre a decisão e a validação visual manual no
Word; mantenha `.mmd`, SVG e PNG intermediários fora de `saidas-mits/`.

Edite a cópia `.docx` localmente com Python e preserve o formato nativo do
Microsoft Word, sem LibreOffice ou conversões intermediárias. Ao escrever Open
XML, clone um parágrafo corporal equivalente para o corpo, nunca a formatação de
um título; preserve tamanho, cor, negrito e paginação e inspecione `rPr`/`pPr`.
Exija validação visual manual no Word. Use linguagem formal, não use emojis e
aplique o Humanizer, ou o fallback local, sem inventar ou alterar fatos.
