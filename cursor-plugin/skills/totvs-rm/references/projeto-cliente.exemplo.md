# projeto-cliente.md (exemplo)

Este arquivo não é uma base do RM e não traz credencial. A skill `totvs-rm`
escolhe o modo cliente quando existe um `projeto-cliente.md` preenchido na
raiz do projeto do usuário. Sem esse arquivo, o modo é local e exige o MCP
de SQL Server com o nome `sqlserver`.

Crie o arquivo real na raiz do projeto em que o agente vai trabalhar, fora
deste pacote. Ele precisa deixar claro que o trabalho é o RM daquele cliente
e que camadas 2 e 3 só podem ser obtidas por sondagem autorizada. Não coloque
senha, connection string, token, nome de base de produção nem dado pessoal.
Não versione esse arquivo em repositório público.
