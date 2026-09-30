---
description: "Consulta dicionário, parametrização e SQL do TOTVS RM sem inventar tabela, campo ou parâmetro"
---

Ative a skill `totvs-rm` e siga o `SKILL.md` dela.

Use `$ARGUMENTS` como a pergunta opcional sobre tabela, campo, módulo, parâmetro ou sentença.

Antes de responder, decida o modo. `projeto-cliente.md` preenchido na raiz do projeto é modo cliente. Sem esse arquivo, o modo é local e exige o MCP de SQL Server com o nome `sqlserver`. Sem os dois, siga `references/configuracao.md` da skill: pergunte o modo e conduza a configuração. Não instale pacote, não grave arquivo e não altere configuração global sem confirmação explícita. Não responda de memória.

Não invente nome de tabela, campo, DataServer ou parâmetro. Camadas 2 e 3 só com consulta ao vivo na base em uso. Não use dado de outra base. Não execute SQL que altere dados, nem crie, altere ou remova sentença, sem confirmação explícita do usuário. Não grave credencial.
