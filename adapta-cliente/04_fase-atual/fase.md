# Fase 3 — Tarefas

<!-- fase-format:2 -->

Cada linha é uma tarefa da Jornada de Execução. **Tudo que cabe num card cabe nesta linha** — se um
campo não estiver aqui, ele não tem como ser preenchido, porque é este arquivo que cria a tarefa.

```
- [ ] Título da tarefa @responsável !30/09/2026 #projeto [interno]   <!-- id:… -->
      > descrição da tarefa, uma ou mais linhas
  - [ ] subtarefa (basta indentar 2 espaços)                         <!-- id:… -->
    - [ ] sub-subtarefa (indente mais 2)                             <!-- id:… -->
```

| marcador | legenda | o que define | se você não escrever |
|---|---|---|---|
| `- [ ]` / `- [/]` / `- [x]` | | a fazer / em andamento / concluída | a fazer |
| `@nome` | | responsável (`@"Nome Composto"` com aspas) | fica **sem responsável** |
| `!dd/mm/aaaa` | | prazo | fica **sem prazo** |
| `#projeto` / `#aculturamento` | | tipo | Projeto de IA |
| `[interno]` | | o cliente **não** vê esta tarefa | o cliente vê |
| `> texto` na linha de baixo | | descrição (aparece ao abrir o card) | sem descrição |
| indentar 2 espaços | | vira subtarefa da tarefa acima (vale em qualquer profundidade) | tarefa de topo |

Os marcadores só valem **no fim da linha** — `Revisar #3 do contrato` continua sendo um título.
Um título que TERMINA na forma de um marcador sai escapado com `\\` (`Ligar para \\@joao`); a barra é
só para o parser e nunca aparece no card. Você não precisa escrever isso à mão.
Marque `[x]` para concluir e adicione linhas novas à vontade: elas entram no quadro na próxima
sincronização e voltam aqui com o `<!-- id:… -->` preenchido. **Não apague o marcador de id** das
tarefas que já têm um.

- [ ] Definir conteúdo aprovado: estrutura da proposta e do folder, fontes e donos @Fábio !03/11/2026  <!-- id:f3a1c2d4-0001-4a01-9a01-000000000001 -->
  > Task T-F3-001 (SPEC-3-001, onda 1, única elegível). Modelar a coleção `conteudos` com RLS, trilha de versão e exclusão bloqueada (padrão Fase 2): proposta e folder como documentos versionados, cada bloco com fonte, vigência e responsável. Insumo embutido (responder dentro da task): quais blocos compõem a proposta (capa, entendimento, escopo, investimento, condições) e do folder (linhas, produtos, alegações), quem aprova cada bloco e qual fonte oficial alimenta cada um. Conteúdo sem fonte/vigência/aprovação nunca visível ao vendedor (padrão RN-2.002). Prova: TDD GREEN com CP-F3-001. Evidência: exportação da coleção + capturas + decisão registrada.
- [ ] Migrar e aprovar o conteúdo piloto, com pendências nomeadas @Fábio !05/11/2026  <!-- id:f3a1c2d4-0002-4a01-9a01-000000000002 -->
  > Task T-F3-002 (SPEC-3-001, onda 2, após T-F3-001). Migrar o conteúdo real (texto da proposta e do folder) com inventário e reconciliação item a item contra as fontes declaradas; aprovar bloco a bloco com ator, data, versão e motivo; pendências nomeadas por bloco incompleto — nada usado em silêncio. Prova: TDD REGRESSÃO com CP-F3-002/003. Evidência: relatório de reconciliação + aceite do champion.
- [ ] Montar a proposta a partir do catálogo aprovado @Fábio !10/11/2026  <!-- id:f3a1c2d4-0003-4a01-9a01-000000000003 -->
  > Task T-F3-003 (SPEC-3-002, onda 3, após T-F3-002). Gerar a proposta a partir do pedido da Fase 1 + catálogo aprovado + motor de investimento da Fase 2: blocos de conteúdo aprovado preenchidos automaticamente, tabela de produtos do pedido com preços vigentes, investimento com componentes separados, condições e validade. Proposta nasce `rascunho`, nunca enviável antes da revisão do vendedor. Prova: TDD GREEN com CP-F3-004. Evidência: exportação da proposta + capturas.
- [ ] Exercitar revisão, versão e rollback da proposta @Fábio !12/11/2026  <!-- id:f3a1c2d4-0004-4a01-9a01-000000000004 -->
  > Task T-F3-004 (SPEC-3-002, onda 4, após T-F3-003). Editar proposta em rascunho com trilha de versão (nova versão ao editar, anterior "Substituído pela proposta X" — padrão Fase 1); revisão do vendedor obrigatória antes de enviar; rollback para a última versão aprovada com recibo auditado. Prova: TDD REGRESSÃO com CP-F3-005/006. Evidência: relatório de cenários + aceite do vendedor.
- [ ] Montar o folder com blocos aprovados e versão @Fábio !17/11/2026  <!-- id:f3a1c2d4-0005-4a01-9a01-000000000005 -->
  > Task T-F3-005 (SPEC-3-003, onda 5, após T-F3-002). Gerar o folder a partir dos blocos de conteúdo aprovado: linhas, produtos e alegações somente com fonte e vigência válidas; versão do folder registrada com trilha; conteúdo vencido fica pendência, nunca entra em silêncio. Prova: TDD GREEN com CP-F3-007. Evidência: exportação do folder + capturas.
- [ ] Exercitar envio com registro, auditoria e recuperação @Fábio !19/11/2026  <!-- id:f3a1c2d4-0006-4a01-9a01-000000000006 -->
  > Task T-F3-006 (SPEC-3-004, onda 6, após T-F3-003 e T-F3-005). Enviar proposta/folder com registro do ato (ator, papel, data, destinatário, versão enviada), auditoria server-side, negativa de envio de versão não aprovada (prova negativa), reenvio idempotente e recuperação após falha (tentativa preservada, reconciliação visível — padrão T-F1-008). Prova: TDD REGRESSÃO com CP-F3-008/009. Evidência: relatório de cenários + aceite do gestor.
- [ ] Estender auditoria, alçadas e fail-closed às superfícies da Fase 3 @Fábio !24/11/2026  <!-- id:f3a1c2d4-0007-4a01-9a01-000000000007 -->
  > Task T-F3-007 (SPEC-3-004, onda 7, após T-F3-006). Hooks de auditoria cobrindo conteúdo, proposta e folder (ator, papel, antes→depois, motivo, versão, resultado); vendedor negado com motivo do servidor; ação sem alçada definida negada por padrão (fail-closed — padrão T-F2-007). Insumo embutido: alçadas de aprovar conteúdo, enviar proposta e reenviar. Prova: TDD GREEN com CP-F3-010/011. Evidência: eventos de auditoria + relatório de negativas.
- [ ] Demonstrar o fluxo ponta a ponta e fechar a Fase 3 @Fábio !26/11/2026  <!-- id:f3a1c2d4-0008-4a01-9a01-000000000008 -->
  > Task T-F3-008 (SPEC-3-004, onda 8, após T-F3-007). Demonstração ponta a ponta: pedido da Fase 1 → proposta montada → revisão → envio com registro → folder com blocos aprovados; fila de pendências de conteúdo com dono e motivo; exportação CSV da trilha (padrão T-F2-008). Handoff ao champion. Prova: TDD REGRESSÃO com CP-F3-012 + handoff. Evidência: recibo de rollback + CSV + aceite do champion.
- [ ] Revisar as evidências e decidir o fechamento da Fase 3 @"Ramon Pádua" !30/11/2026 #aculturamento  <!-- id:f3a1c2d4-0009-4a01-9a01-000000000009 -->
  > Revisão do consultor: conferir evidências e aceites das 8 tasks, validar as decisões do handoff da Fase 2 e decidir o fechamento formal da Fase 3 com gate de transição para a Fase 4.
