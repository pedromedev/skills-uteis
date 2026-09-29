# Base de conhecimento — camada 4

Só semântica e comportamento do produto TOTVS: o que uma rotina faz, o
domínio de valores de um campo, regras do produto. **Estrutura não entra
aqui** (está em `references/dicionario.md`), e **dado de cliente nunca
entra** — nem campo complementar, nem parâmetro, nem código cadastrado.

## Organização

Um arquivo por assunto: `<modulo>/<assunto>.md`. Granularidade fina é
proposital: duas pessoas quase nunca tocam o mesmo arquivo, então
conflito de merge some. Para achar algo, busque — não leia a pasta
inteira.

## Formato de item

    ## <Assunto>

    - **Nível:** ✅ confirmado-rm | 📘 oficial-totvs | ⚠️ nao-confirmado
    - **Evidência:** <base e versão do RM, ou URL da página lida>
    - **Data:** AAAA-MM-DD

    <o conteúdo>

Item ⚠️ é registrado de propósito — preserva a pesquisa para ninguém
repetir — mas **não entra em código nem em resposta afirmativa**.

Todo item traz data e evidência. Sem isso não há como saber, depois de
uma atualização de versão, se ainda vale.

## O teste antes de registrar algo aqui

Pergunte: **esse item continua válido se o cliente mudar amanhã?**
"PFUNC.CODSITUACAO tem domínio parametrizável, valores usuais A/D/F/T,
confirme na base antes de filtrar" passa — é sobre o produto. "O cliente
tem os códigos A e D cadastrados" não passa — é dado de camada 2/3
disfarçado de conhecimento. Dado de um cliente específico não entra nesta
pasta.

## Contribuição

Atualize esta pasta em branch, commit e pull request. Não grave direto na
branch principal. Não registre nome de cliente, arquivo interno nem credencial.
