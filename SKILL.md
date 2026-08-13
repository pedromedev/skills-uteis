---
name: escrever-mits
description: "Entrevista analistas e transforma fontes de projeto em MITs TOTVS, com template Word escolhido pelo caminho exato, revisão seção a seção, rastreabilidade e saída Word revisável."
---

# Skill universal para escrever MITs TOTVS

## Objetivo e decisão de escopo

Use esta skill para entrevistar o analista responsável e transformar informações
de um projeto em uma MIT (Metodologia de Implantação TOTVS). A decisão sobre a
adequação da MIT às necessidades do projeto é sempre do analista. A skill não
aprova, valida, certifica ou declara que o documento está pronto para uso.

O resultado final deste fluxo é sempre um arquivo **Word** (`.docx`) preenchido
em uma cópia do template Word indicado, acompanhado de um relatório de
pendências em Markdown. Excel e PDF podem ser fontes de informação, mas nunca
substituem o template Word nem são a saída deste fluxo.

## Entradas e preparação

1. Use `contexto/` na raiz do projeto como pasta de contexto por padrão. Aceite
   uma pasta alternativa quando o analista informar seu caminho exato.
2. Procure e leia, sem alterar, os arquivos relevantes de Word, Excel e PDF da
   pasta de contexto. Registre o nome/caminho, tipo, data ou versão aparente,
   páginas/abas relevantes e o que foi extraído de cada fonte.
3. Exija o caminho exato do template **Word** (`.docx`). Não selecione um
   template apenas pelo código MIT, pelo nome mais parecido ou por inferência.
   Se o caminho não existir, estiver ambíguo ou apontar para Excel/PDF, pare e
   peça a correção.
4. Antes da entrevista, inspecione o template sem editá-lo: seções, títulos,
   parágrafos, tabelas, células, cabeçalho, rodapé, estilos, campos,
   marcadores, imagens e áreas que parecem preenchíveis. Registre a ordem das
   seções e os locais que receberão cada informação.
5. Se qualquer arquivo estiver ilegível, protegido por senha, corrompido ou
   não puder ser interpretado com segurança, não o trate como fonte confirmada.
   Explique o bloqueio e solicite ao analista que envie prints legíveis na
   conversa (ou forneça outra cópia/autorização). Registre a limitação.

### Catálogo local de referência

O catálogo abaixo é apenas um exemplo do repositório em que esta cópia da skill
está instalada. Em outro repositório, a pasta, os nomes e os caminhos podem
mudar; confirme o caminho exato informado pelo analista e não presuma que este
catálogo esteja disponível.

- **MIT037 — Roteiro de Capacitação**
  - `templates-mits-analista/MIT037 - Roteiro de Capacitação/MIT037 - Roteiro de Capacitação_(Modelo VDocAgo2025).xlsx`
  - `templates-mits-analista/MIT037 - Roteiro de Capacitação/MIT037 -Cópia de Roteiro de Capacitação.xlsx`
- **MIT041 — Diagrama de Processo**
  - `templates-mits-analista/MIT041 - Diagrama de Processo/MIT041 - Diagrama dos processos_(Modelo VDocJul2025).docx`
- **MIT041 — Escopo Técnico SFA**
  - `templates-mits-analista/MIT041 - Escopo Técnico SFA/MIT041 - Escopo Técnico - TOTVS CRM - SFA_(Modelo VDocJul2025).docx`
- **MIT041 — Diagrama de processo — Migração de Dados**
  - `templates-mits-analista/MIT041 - Diagrama de processo - Migração de Dados/MIT041 - Diagrama dos processos_Estratégia de Migração - Educacional_Modelo.docx`
  - `templates-mits-analista/MIT041 - Diagrama de processo - Migração de Dados/MIT041 - Diagrama dos processos_Estratégia de Migração - Macro - Oficial_Modelo.docx`
  - `templates-mits-analista/MIT041 - Diagrama de processo - Migração de Dados/MIT041 - Diagrama dos processos_Estratégia de Migração - Folha - Oficial_Modelo.docx`
  - `templates-mits-analista/MIT041 - Diagrama de processo - Migração de Dados/MIT041 - Diagrama dos processos_Estratégia de Migração - BO -Oficial_Modelo_.docx`
- **MIT043 — Plano de Configuração (Parametrização do Sistema) — RM**
  - `templates-mits-analista/MIT043 - Plano de Configuração (Parametrização do Sistema)- RM/MIT043 - Plano de Configuração (Parametrização do Sistema)  RM.xlsx`
- **MIT044 — Especificação da Customização**
  - `templates-mits-analista/MIT044 - Especificação da Customização/MIT044 - Especificação da Customização_(Modelo VDocJul2025).docx`
- **MIT045 — Roteiro de Testes**
  - `templates-mits-analista/MIT045 - Roteiro de Testes/MIT045 - Roteiro de Testes.xlsx`
  - `templates-mits-analista/MIT045 - Roteiro de Testes/MIT045 - Roteiro de Testes - Simplificado.xlsx`

Neste catálogo, MIT041 possui mais de uma finalidade e variante. Confirme com
o analista qual documento pretende produzir; nunca escolha uma variante por
inferência. Os arquivos Excel listados são referência de fontes ou de outros
fluxos, não templates válidos para a saída desta skill.

## Regras de conteúdo, fontes e segurança

- Use como fato somente o que estiver em uma fonte legível ou o que o analista
  declarar explicitamente. Não invente escopo, regras, prazos, responsáveis,
  integrações, campos, critérios de aceite, versões ou dados.
- Diferencie no registro: fato extraído (`F`), resposta do analista (`R`),
  decisão (`D`), inferência não confirmada e sugestão. Inferências e sugestões
  não podem ser apresentadas como requisitos.
- Quando faltar informação, houver dúvida ou a confirmação não tiver ocorrido,
  escreva exatamente `A confirmar` e crie uma pendência com contexto e fonte
  (ou indique que não há fonte). É permitido gerar o documento com `A
  confirmar`, desde que todas as pendências fiquem destacadas no Markdown e
  sejam visíveis no Word quando couber.
- Não escolha entre fontes conflitantes pela data, pelo tom ou pela
  plausibilidade. Mostre ao analista as versões conflitantes, seus locais e o
  impacto; pergunte qual deve prevalecer (ou peça uma nova decisão) e registre
  a resolução.
- Processe arquivos localmente por padrão. Antes de enviar qualquer dado do
  cliente, print ou documento para serviço externo, peça autorização explícita,
  informando o que será enviado, para onde e por quê. Sem autorização, não
  faça o envio.
- Não exponha credenciais, tokens ou dados além do necessário. Trate prints e
  arquivos do cliente como informação sensível.
- Não declare aprovação, validação, conformidade, completude ou adequação
  automática. Relate apenas verificações efetivamente realizadas e lembre que
  a revisão e a decisão final são do analista.

## Fluxo obrigatório de entrevista

### 1. Receber contexto e template

Leia a intenção e `$ARGUMENTS` quando o ambiente fornecer esse valor. Aceite
argumentos contendo o caminho do template e/ou uma pasta alternativa de
contexto. Se faltar qualquer um deles, pergunte somente o que falta. Confirme
o código MIT, a variante, o nome do projeto, o caminho da pasta de contexto e o
caminho exato do template Word.

### 2. Ler fontes e inspecionar o template

Faça o inventário de Word, Excel e PDF em `contexto/` (ou na pasta alternativa)
e leia o conteúdo relevante. Excel e PDF são fontes: não os converta
automaticamente para a saída e não copie sua aparência como se fosse estrutura
do Word. Depois inspecione o template Word e monte um mapa de seções na ordem
em que aparecem. Não faça perguntas em lote antes de saber quais seções e
campos existem.

Se uma proteção ou ilegibilidade impedir uma leitura confiável, solicite prints
na conversa e aguarde. Marque o material como não confirmado até que o analista
o esclareça.

### 3. Produzir o rascunho

Entreviste em pequenos blocos, começando pelas primeiras seções do template.
Use as fontes e respostas para preparar um rascunho, sem ainda tratá-lo como
aprovado. Para cada seção mantenha:

- conteúdo proposto e local correspondente no template;
- fontes e identificadores de evidência (`F-001`, `R-001`, `D-001` etc.);
- lacunas `A confirmar`;
- conflitos, perguntas e correções;
- estado: `pendente`, `correção solicitada` ou `confirmada`.

Faça perguntas curtas e concentradas na seção atual. Se uma resposta alterar
uma seção já confirmada, informe o impacto e peça autorização para reabrir essa
seção específica; não a altere silenciosamente.

### 4. Revisar seção por seção

Apresente cada seção na ordem do template, uma por vez, com o texto proposto,
`A confirmar` e as pendências pertinentes. Ao final de cada apresentação,
pergunte explicitamente: **“Esta seção está OK ou deseja alguma correção?”**

- Só avance depois de um `OK` explícito para a seção atual.
- Se o analista pedir correção, registre a solicitação, reescreva a seção e
  apresente-a novamente. Repita até receber `OK` explícito.
- Nunca transforme silêncio, resposta vaga ou confirmação de outra seção em
  confirmação da seção atual.
- Preserve as seções confirmadas; só as reabra mediante solicitação explícita e
  registre a nova versão e o motivo.
- Em conflito, interrompa o avanço daquela decisão, apresente as alternativas
  e pergunte ao analista. A resolução precisa ficar registrada.

### 5. Revisão completa e autorização final

Depois que todas as seções estiverem confirmadas, mostre um resumo completo do
documento: identificação, fontes usadas, conteúdo por seção, `A confirmar`,
conflitos resolvidos, pendências e limitações de preservação. Peça correções
adicionais se houver qualquer divergência.

Quando o resumo estiver correto, peça uma autorização final, separada dos `OK`
de cada seção, para gravar a cópia Word e o Markdown. Sem essa autorização,
não grave a saída final nem diga que ela foi gerada.

## Saída, nomenclatura e limites de edição

1. Crie `saidas-mits/` se necessário e escreva somente ali. Nunca edite,
   sobrescreva, renomeie ou mova o arquivo original em
   `templates-mits-analista/`; trate todo template e toda fonte como somente
   leitura.
2. Copie o template Word e edite apenas a cópia autorizada. Preserve a
   estrutura, seções, tabelas, cabeçalho, rodapé, estilos, numeração, campos,
   imagens e quebras do template. Preencha locais existentes e apropriados;
   não crie uma nova estrutura para esconder um campo ausente e não faça
   alterações cosméticas ou estruturais não solicitadas.
3. Se o formato ou a ferramenta não permitir preservar com segurança um local,
   deixe `A confirmar` ou uma pendência e descreva a limitação. Não simule uma
   edição concluída.
4. Use o identificador `CODIGOMIT-PROJETO-DATA.docx`, com o código MIT, um
   projeto sanitizado sem separadores de caminho e a data da geração (de
   preferência `AAAA-MM-DD`). Exemplo: `MIT044-INTEGRACAO-RM-2026-08-13.docx`.
   Se o mesmo identificador já existir, use `-v2`, depois `-v3` e assim por
   diante, antes da extensão. Não substitua uma versão anterior.
5. Gere, junto ao Word, `CODIGOMIT-PROJETO-DATA.md` com o mesmo identificador
   (incluindo `-v2` etc.). O Markdown deve listar template e versão, fontes,
   seções confirmadas, itens `A confirmar`, conflitos e resoluções,
   pendências, limitações, verificações realizadas e a declaração de que a
   revisão/adequação/aprovação final cabe ao analista.
6. A saída é sempre Word. Se o analista pedir Excel ou PDF como produto final,
   explique que eles podem ser fontes e peça um template Word válido.

## Verificação e entrega

Após a autorização e a gravação, faça somente verificações que a ferramenta
local consiga sustentar: existência e abertura do `.docx`, correspondência com
o template, presença das seções/campos tratados e preservação observável da
estrutura. Registre o que foi verificado e o que não foi possível verificar;
nunca converta uma limitação em declaração de sucesso.

Entregue os caminhos relativos do Word e do Markdown, o caminho exato do
template usado, a versão gerada, o resumo de pendências e as limitações. Diga
explicitamente que o documento é um rascunho/documento para revisão e que a
decisão final de adequação, validação e aprovação é do analista.

Depois da entrega, se o analista pedir reabertura, pergunte quais seções foram
indicadas e reabra **somente** essas seções. Não revise novamente as demais por
iniciativa própria. Registre as mudanças e gere uma nova versão, sem substituir
o arquivo já entregue.
