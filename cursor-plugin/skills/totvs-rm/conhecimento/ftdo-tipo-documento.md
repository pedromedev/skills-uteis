# FTDO — Tipos de Documento (Fluxus)

- **Nível:** validado em base de referência
- **Evidência:** base de referência, `dbo.GDIC` + `sys.columns` + amostra `FTDO`, 2026-09-22
- **Tabela:** `dbo.FTDO` (linha `#` = "Tipos de Documento")
- **Chave relevante:** `CODCOLIGADA` + `CODTDO`
- **Descrição:** coluna física `DESCRICAO` — **não existe** `DESCTDO` no dicionário
- **Apresentação:** não existe coluna `DESCTDO`. Para exibir código e descrição, use alias, por exemplo `DESCRICAO AS DESCTDO`
- **Filtro:** `CODCOLIGADA = :CODCOLIGADA`; opcional `ISNULL(INATIVO,0)=0`
