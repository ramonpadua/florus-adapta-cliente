# Fase 2 — Tarefas

<!-- fase-format:2 -->

Cada linha é uma tarefa da Jornada de Execução. **Tudo que cabe num card cabe nesta linha** — se um
campo não estiver aqui, ele não tem como ser preenchido, porque é este arquivo que cria a tarefa.

```
- [ ] Título da tarefa @responsável !30/09/2026 #projeto [interno]   <!-- id:… -->
      > descrição da tarefa, uma ou mais linhas
  - [ ] subtarefa (basta indentar 2 espaços)                         <!-- id:… -->
    - [ ] sub-subtarefa (indente mais 2)                             <!-- id:… -->
```

| marcador | o que define | se você não escrever |
|---|---|---|
| `- [ ]` / `- [/]` / `- [x]` | a fazer / em andamento / concluída | a fazer |
| `@nome` | responsável (`@"Nome Composto"` com aspas) | fica **sem responsável** |
| `!dd/mm/aaaa` | prazo | fica **sem prazo** |
| `#projeto` / `#aculturamento` | tipo | Projeto de IA |
| `[interno]` | o cliente **não** vê esta tarefa | o cliente vê |
| `> texto` na linha de baixo | descrição (aparece ao abrir o card) | sem descrição |
| indentar 2 espaços | vira subtarefa da tarefa acima (vale em qualquer profundidade) | tarefa de topo |

Os marcadores só valem **no fim da linha** — `Revisar #3 do contrato` continua sendo um título.
Um título que TERMINA na forma de um marcador sai escapado com `\\` (`Ligar para \\@joao`); a barra é
só para o parser e nunca aparece no card. Você não precisa escrever isso à mão.
Marque `[x]` para concluir e adicione linhas novas à vontade: elas entram no quadro na próxima
sincronização e voltam aqui com o `<!-- id:… -->` preenchido. **Não apague o marcador de id** das
tarefas que já têm um.

- [x] Definir fonte oficial, linha piloto e modelar o cadastro governado de produtos @Fábio !07/10/2026  <!-- id:76c41319-7f1f-4b52-a374-698df786c7ef -->
  > Task T-F2-001 (SPEC-2-001, onda 1, única elegível). Modelar a coleção `produtos` com RLS, trilha de versão e exclusão bloqueada; cadastrar a linha piloto com fonte, vigência e responsável. Insumo embutido (responder dentro da task): qual é a fonte oficial do catálogo (ERP, Excel controlado ou cadastro governado) e qual linha/produto piloto migrar primeiro? Pendências nomeadas em campo ausente; item sem fonte/aprovação nunca visível ao vendedor. Prova: TDD GREEN com CP-F2-001. Evidência: exportação da coleção + capturas + decisão registrada.
- [x] Migrar e reconciliar a linha piloto, com higiene de duplicatas e emenda do toast @Fábio !09/10/2026  <!-- id:d7a75d81-3432-45a1-8256-2036e736e4a5 -->
  > Task T-F2-002 (SPEC-2-001, onda 2, após T-F2-001). Migrar a linha piloto com inventário e reconciliação item a item contra a fonte declarada; duplicata legada 20260921-0003 marcada como LIMPEZA-PROVA (esconde da lista, mantém histórico — aceite D4 do handoff); emenda de UX: duração do toast de confirmação ~12s (aceite D3). Duplicidade de SKU bloqueada; substituição com trilha `substituido_por`. Prova: TDD REGRESSÃO com CP-F2-002/003. Evidência: relatório de reconciliação + aceite do champion. **Concluída em 2026-10-14:** 104 itens migrados e aprovados (todas as linhas — emenda AC-010), reconciliação 104/104, correções das considerações do champion (ID/SKU numéricos, botão Editar, nomes originais, linhas realocadas, ID real na Industrialização), revalidação de encerramento 6/6, aceite "Testei o ID de industrialização e funcionou. Também testei o Catálogo e ele está correto."
- [x] Configurar composição, conversão e cálculo de lote @Fábio !13/10/2026  <!-- id:21ba44a4-e679-46fc-bf01-232146f0ba75 -->
  > Task T-F2-003 (SPEC-2-002, onda 3, após T-F2-001). Linhas de composição vinculadas ao pedido da Fase 1 com RLS; conversão com a fórmula cadastrada; comparação com lote mínimo mostrando diferença e quantidade sugerida. Insumo embutido (responder dentro da task): fórmulas de conversão/densidade por produto e precedência entre 1.000 unidades/SKU × mínimo em kg. Sem densidade/fórmula, linha fica `nao_calculavel` com pendência nomeada — nunca viável, nunca trava as demais. Prova: TDD GREEN com CP-F2-004. Evidência: exportação da composição + capturas.
- [x] Exercitar mínimo, precedência, exceções e linhas não calculáveis @Fábio !15/10/2026  <!-- id:5d366136-682a-4d03-8b7d-cf7fb76a9d84 -->
  > Task T-F2-004 (SPEC-2-002, onda 4, após T-F2-003). Exercitar quantidade abaixo do mínimo (sugestão + ajuste com motivo auditado), conflito de precedência (regra aplicada exibida; sem regra, `revisao_necessaria`) e linha inválida sem contaminar as demais. Prova: TDD REGRESSÃO com CP-F2-005/006. Evidência: relatório de cenários + aceite do vendedor/técnico.
- [x] Revisar as evidências e decidir o fechamento da Fase 2 @"Felipe Navaar" !31/10/2026 #aculturamento  <!-- id:ac32fc45-6869-4b75-ad7b-e7002cf6498c -->
  > Revisão do consultor: conferir evidências e aceites das 8 tasks, validar as decisões D1 (fórmula de acurácia — segue para a Fase 3) e D2 (meta/tolerância do baseline) do handoff da Fase 1 e decidir o fechamento formal da Fase 2 com gate de transição para a Fase 3. **Concluída em 2026-10-26:** revisão executada pelo consultor responsável (Ramon Pádua) — 8/8 evidências e aceites conferidos no repositório; D1/D2 já aprovadas pelo champion em 02/10; fechamento formal registrado em check-fase-2.md; gate para a Fase 3 LIBERADO.
- [x] Configurar cálculo de investimento, componentes e comparação com política @Fábio !20/10/2026  <!-- id:177754f9-5663-48bb-b8a0-beadb589432e -->
  > Task T-F2-005 (SPEC-2-003, onda 5, após T-F2-003). Total por linha e por projeto com componentes separados (formulação, embalagem, frete, tributos, taxas, margem) e incluído/excluído/pendente explícito; comparação com a política de qualificação/investimento mostrando a regra usada. Insumo embutido (responder dentro da task): componentes e regras de custo, política de preço/piso e semântica dos limiares. Fonte divergente → `revisao_de_preco` preservando a versão anterior. Prova: TDD GREEN com CP-F2-007. Evidência: exportação do cálculo + capturas.
- [x] Exercitar duas aprovações, exceções e rollback de preço @Fábio !22/10/2026  > Task T-F2-006 (SPEC-2-003, onda 6, após T-F2-005). Alteração de preço com duas aprovações, histórico, vigência e rollback; preço sem as duas aprovações permanece indisponível para consumo (prova negativa); exceção com motivo, responsável e alçada; negativa auditada com motivo do servidor. Prova: TDD REGRESSÃO com CP-F2-008/009. Evidência: trilha de preço + relatório de cenários + aceite do gestor/Financeiro. **Concluída em 2026-10-23:** aceite do champion "Testei e está aprovado" (Partes A–D OK 22/10; Parte E esclarecida — exceção da trilha = preço de tabela com alçada admin; exceção POR PEDIDO fica p/ política I6) + emendas de usabilidade v0.0.145 aprovadas (busca inteligente, ordenador multi-campo, seletor pesquisável); revalidação de encerramento TDD 22/22 PASS contra o sistema real.
- [x] Estender auditoria, matriz de alçadas e fail-closed às superfícies da Fase 2 @Fábio !27/10/2026  <!-- id:99f2fa5d-79e6-4392-a6d0-dcf204f590a2 -->
  > Task T-F2-007 (SPEC-2-004, onda 7, após T-F2-005). Hooks de auditoria cobrindo catálogo, composição, investimento e preço (ator, papel, antes→depois, motivo, versão, resultado); vendedor negado com motivo do servidor; ação sem alçada definida negada por padrão (fail-closed). Insumo embutido (responder dentro da task): matriz nominal de alçadas da Fase 2 — quem aprova preço, exceção e migração. Prova: TDD GREEN com CP-F2-010/011. Evidência: eventos de auditoria + relatório de negativas.
- [x] Demonstrar rollback transversal, fila de pendências e exportação da trilha @Fábio !29/10/2026  <!-- id:0ea54893-fcb7-4260-a464-c676f59ca8c6 -->
  > Task T-F2-008 (SPEC-2-004, onda 8, após T-F2-007). Rollback de qualquer objeto da Fase 2 restaurando a última versão aprovada com recibo e zero vigentes duplicadas; fila de pendências da calculadora com dono e motivo; exportação CSV da trilha sanitizada. Handoff ao champion com demonstração ponta a ponta da calculadora. Prova: TDD REGRESSÃO com CP-F2-012 + handoff. Evidência: recibo de rollback + CSV + aceite do champion. **Concluída em 2026-10-26:** aceite do champion "Deu certo" após teste completo (itens 1–4: Pendências visíveis com 2 itens reais, CSV avaliado e aprovado pelo champion, rollback na trilha com motivo na própria linha — emenda v0.0.164, vendedor sem acesso); revalidação de encerramento 18/18 PASS contra o sistema real; achado corrigido: 9 vigentes duplicadas na trilha → 1 (migration 0031).
