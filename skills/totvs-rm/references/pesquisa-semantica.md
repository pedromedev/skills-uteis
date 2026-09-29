# Pesquisa semântica

Quando a pergunta é sobre **comportamento**, não estrutura — o que uma
rotina faz, o domínio de valores de um campo, uma regra de negócio do
produto. O dicionário (camada 1) dá nome e rótulo curto; não dá
comportamento nem domínio completo. `PFUNC.CODSITUACAO` tem 23 valores
nesta base e o `GDIC` só diz "Código da Situação" — o significado de cada
código é semântica, não estrutura.

## Hierarquia de fontes

1. **✅ Confirmado no RM** — testado contra uma base real: valor observado,
   comportamento reproduzido, sentença executada. A fonte mais forte, e a
   única que esta skill trata como fato sem ressalva.
2. **📘 Oficial TOTVS** — página do TDN (Totvs Developer Network) ou da
   Central de Atendimento, **efetivamente aberta e lida** no navegador.
3. **⚠️ Não confirmado** — pista de terceiros (fórum, blog, resposta de
   IA), ou página oficial localizada mas não lida. Registra-se para não
   repesquisar, mas **não entra em código nem em afirmação na resposta**.

Um resultado de busca que só mostra o snippet não sobe a nível 2. TDN e
Central de Atendimento **bloqueiam fetch automatizado** — buscar serve
apenas para localizar a URL; só a leitura da página conta como confirmação.

## Fluxo

1. A pergunta é sobre estrutura? Vá para `dicionario.md` ou consulta ao
   vivo (camadas 1–2), não aqui.
2. É sobre conteúdo de uma base específica (parâmetro, código cadastrado)?
   Camada 3, consulta ao vivo — não aqui.
3. É sobre comportamento do produto? Busque primeiro em `conhecimento/` —
   pode já estar registrado, com nível e data.
4. Não está lá? Busque na documentação oficial. Abra a página, não confie
   no resultado de busca. Se a página confirmar, registre em
   `conhecimento/` com nível 📘, URL e data.
5. Nada encontrado, ou só pista de terceiros? Registre ⚠️ com a fonte, e
   **diga ao usuário que não está confirmado** — não afirme como se fosse.

## Por que isso fica fora do dicionário

O dicionário muda por versão e é reconsultável a qualquer momento; a
semântica do produto é estável entre versões e cara de redescobrir — vale
a pena documentar uma vez. Misturar as duas faria a base de conhecimento
reafirmar como fato estrutural algo que é interpretação, e vice-versa.
