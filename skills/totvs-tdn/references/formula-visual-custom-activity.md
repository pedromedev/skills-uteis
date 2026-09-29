# Atividade customizada de Fórmula Visual (RM)

TDN oficial: [Criando uma nova atividade de Fórmula visual](https://tdn.totvs.com/pages/viewpage.action?pageId=160105553)

O guia é voltado à criação de uma nova atividade de Fórmula Visual no RM e cobre:

- referências a `RM.Lib.dll`, `RM.Lib.WinForms.dll` e `RM.Lib.Workflow.Activities.dll`;
- uma classe que herda de `RMSActivity` ou de uma derivada, como `RMSDynamicActivity`;
- implementação da regra de negócio sobrescrevendo `Execute`;
- compilação em modo Release e cópia da DLL gerada para a biblioteca do RM;
- cadastro da atividade no RM informando nome completo da classe e nome do assembly.

O TDN traz imagens do fluxo e exemplos de uso de DataServer. A TOTVS ressalta que a atividade é responsabilidade do cliente. Se a DLL for removida da pasta de binários (por exemplo, `RM.Net`), a atividade pode deixar de ser válida/executada nas Fórmulas Visuais.

O documento informa escopo para Framework/RM nas versões 11.82.xx e 12.01.xx, publicado em 04/09/2014. Ao responder, sinalize que o usuário deve confirmar aderência à versão atual do seu RM.
