# Modo cliente

Canal validado ponta a ponta contra um RM real em 2026-07-29. O resultado
foi **viável**, com o contrato técnico documentado em `deploy-sentenca.md`.

## Quando este modo se aplica

Existe um `projeto-cliente.md` preenchido na raiz do projeto atual. Sem
esse arquivo, é modo local — ver `../SKILL.md`. O predicado é observável de
propósito: não depende de julgamento sobre "isso parece ambiente de
cliente".

O arquivo real fica na raiz do projeto do usuário, não dentro da skill. Quem
ainda não tem esse arquivo segue `configuracao.md`: o agente pergunta endereço
do RM, nome da base e ambiente (`homologação` ou `produção`) e grava
`projeto-cliente.md` a partir de `projeto-cliente.template.md`, nesta pasta,
só depois da confirmação. O arquivo não leva senha, usuário, token nem
connection string. `projeto-cliente.exemplo.md` explica o que ele não é.

## A regra dura

**Camada 2 e 3 nunca vêm de outra base — nem da de desenvolvimento, nem da
de outro cliente, por mais parecida que seja.** A estrutura do RM (camada
1, `dicionario.md`) é estável entre clientes desta versão; o conteúdo, não.
Responder sobre um cliente com dado de outra base é a falha central que
este modo existe para impedir, mesmo que a resposta pareça bem
fundamentada — o repositório documenta o RM, não *este* RM.

## Ciclo de sondagem, em quatro passos

Sempre nesta ordem, sempre os quatro. Nenhum passo é opcional quando os
outros três acontecem.

### 1. Autorizar

Antes de qualquer escrita no ambiente do cliente — mesmo que o SQL dentro
da sentença seja `SELECT` puro. **Criar o registro é escrita**, mesmo que
o conteúdo seja leitura: grava linha em `dbo.GCONSSQL`, fica visível a
quem tiver o perfil correspondente, e roda com as credenciais usadas na
sessão.

Texto sugerido para pedir autorização:

> Para descobrir [estrutura/parametrização] neste cliente, preciso criar
> uma sentença de sondagem temporária no RM dele, executá-la, e removê-la
> em seguida. Confirma que posso prosseguir?

Não aceitar "é só leitura" ou "pode subir, eu autorizo" como substituto de
confirmação explícita — a decisão é sobre a escrita do objeto, não sobre o
conteúdo do `SELECT`.

### 2. Criar

`POST /RMSRestDataServer/rest/GlbConsSqlData` — corpo e estrutura em
`deploy-sentenca.md`. **`CODSENTENCA` tem limite de 16 caracteres**;
estourar dá erro genérico sem apontar o campo (ver armadilha 1 de
`deploy-sentenca.md`).

### 3. Executar

`GET /api/framework/v1/consultaSQLServer/RealizaConsulta/{cod}/{coligada}/{aplicacao}?parameters=...`
— devolve array JSON puro (sem envelope). As linhas retornadas são a
evidência; cite-as, não parafraseie de memória.

### 4. Remover, e confirmar que sumiu

`DELETE /RMSRestDataServer/rest/GlbConsSqlData/{pk}`, com a chave no
formato `CODCOLIGADA$_$APLICACAO$_$CODSENTENCA` percent-encoded.

**A limpeza só está concluída quando uma nova execução falha.** Repita o
passo 3 depois do `DELETE`: esperado `404` com `code: "FE011"`. Reportar
"removido" sem essa confirmação não é remoção verificada — é intenção.

> Cenário observado: sob pressão para encerrar, um agente sem esta regra
> argumentou corretamente a favor de remover e cedeu na última frase. A
> limpeza não é preferência de quem pede.

## Convenção de nomenclatura da sentença de diagnóstico

Prefixo `DIAG.` seguido de identificador curto — **16 caracteres no
total**, contando o prefixo. `DIAG.DICIONARIO.01` (18 caracteres) **não
cabe** e foi o erro que causou HTTP 500 na validação deste canal.
`DIAG.DIC.01` (11 caracteres) funciona. O prefixo identificável torna a
sondagem reconhecível para não colidir com sentença do cliente e para a
limpeza ser auditável.

## Fallback sem escrita

Sem autorização de escrita, ou quando ela for negada: usar apenas
sentenças e DataServers **já cadastrados** no ambiente do cliente,
lendo `GET /RMSRestDataServer/rest/GlbConsSqlData` (a listagem devolve o
texto SQL de cada sentença existente) e as chaves retornadas por outros
DataServers já expostos. Enxerga pouco — só o que já foi escrito por
alguém — mas tem pegada zero: não cria nada, não precisa de limpeza.

> Evitar `GET` sem filtro em bases grandes: a listagem completa devolveu
> 2 MB e 829 sentenças nesta validação. Prefira buscar por chave quando
> souber o `CODSENTENCA`, ou trate a listagem completa como último recurso.

## Se a resposta ao `POST` não for a esperada

O canal responde 500 para qualquer erro, **sempre com o motivo no corpo**
— nunca conclua causa a partir só do código HTTP. Ver armadilha 2 de
`deploy-sentenca.md`. Numa falha real, um diagnóstico de "canal rejeitando
na entrada" foi construído sobre a leitura errada de um corpo que, lido
direito, continha a causa exata.
