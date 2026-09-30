# projeto-cliente.md (exemplo)

Este arquivo não é uma base do RM e não traz credencial. A skill `totvs-rm`
escolhe o modo cliente quando existe um `projeto-cliente.md` preenchido na
raiz do projeto do usuário. Sem esse arquivo, o modo é local e exige o MCP
de SQL Server com o nome `sqlserver`.

O agente cria o arquivo real na raiz do projeto, fora da skill, a partir de
`projeto-cliente.template.md`. O modelo pede endereço do RM, nome da base e
ambiente (`homologação` ou `produção`). Não coloque senha, usuário, token,
connection string nem dado pessoal. Não versione esse arquivo em repositório
público. Ambiente de produção não dispensa a autorização de `modo-cliente.md`
antes da sondagem.
