---
description: "Levanta o contexto técnico no RM e no TDN e depois redige a MIT"
---

Execute o fluxo das três skills, nesta ordem.

Use `$ARGUMENTS` como o pedido do documento. Pode trazer o assunto, o código da MIT e o caminho exato do template Word.

1. Ative `totvs-tdn` e registre título, link, versão visível e limites do TDN oficial. Se não houver guia, registre a ausência.
2. Ative `totvs-rm`. Confirme tabela, campo, relacionamento e parâmetro na base em uso. Sem MCP `sqlserver` e sem `projeto-cliente.md`, pare. Não invente objeto do RM e não execute SQL de escrita sem confirmação explícita do usuário.
3. Grave o que foi confirmado em `contexto/ENTREVISTA-DOCUMENTO.md`. Lacuna fica como `A confirmar`, com a fonte ou a indicação de que não há fonte.
4. Ative `escrever-mits`. Exija o caminho exato do template Word, revise cada seção com o analista e só gere o `.docx` e as pendências em `saidas-mits/` depois da autorização final.

A decisão de adequação do documento é do analista. Não declare aprovação automática.
