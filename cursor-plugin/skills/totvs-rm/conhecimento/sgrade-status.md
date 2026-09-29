# SGRADE.STATUS — Status da Matriz Curricular

- **Nível:** Confirmado no RM (enum + base de referência) + Oficial TOTVS (TDN)
- **Data:** 2026-09-22
- **Tabela:** `dbo.SGRADE` (Grade Curricular / Matriz Curricular)
- **DataServer:** `EduGradeData` (objeto `SGrade`)
- **Campo GDIC:** `STATUS` — "Status da Matriz Curricular" (`varchar(1)`)

## Domínio (enum `RM.Edu.Consts.EduStatusMatrizCurricularEnum`)

| Código | Label | Semântica (TDN) |
|--------|-------|-----------------|
| `0` | Ativa | Pode ser usada na matrícula |
| `1` | Inativa | Não pode ser utilizada |
| `2` | Atual | Preenchida automaticamente na matrícula; usuário pode trocar para outra Ativa |

## Evidências

1. Reflection no assembly `RM.Edu.Consts.dll`, enum `EduStatusMatrizCurricularEnum` (Ativa=0, Inativa=1, Atual=2)
2. Base de referência: 9 linhas em `SGRADE`, todas `STATUS = 0` (sem amostra de 1/2 nesta base)
3. TDN Matriz Curricular: https://tdn.totvs.com/x/3-xbGQ (três status Ativa/Inativa/Atual, sem códigos na página)
