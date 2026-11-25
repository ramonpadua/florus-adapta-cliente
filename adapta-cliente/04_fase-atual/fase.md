# Fase 3 — Tarefas

<!-- fase-format:2 -->

Cada linha é uma tarefa da Jornada de Execução. **O detalhe narrativo de cada task concluída está no relatório correspondente em `05_entregas/`** — aqui fica o contrato: título, responsável, prazo, id, escopo em uma linha e o ponteiro da evidência.

```
- [ ] Título da tarefa @responsável !30/09/2026 #projeto [interno]   <!-- id:… -->
      > descrição da tarefa
```

| marcador | legenda |
|---|---|
| `@` | responsável |
| `!` | prazo |
| `#` | projeto/etiqueta |

## Tarefas

- [x] Definir conteúdo aprovado: estrutura da proposta e do folder, fontes e donos @Fábio !03/11/2026 <!-- id:f3a1c2d4-0001-4a01-9a01-000000000001 -->
  > 8 blocos de proposta e 5 de folder; aprovação por gestor/admin com fonte, conferência e vigência. Concluída 28/10 — `05_entregas/T-F3-001-conteudo-aprovado.md`.

- [x] Migrar e aprovar o conteúdo piloto, com pendências nomeadas @Fábio !05/11/2026 <!-- id:f3a1c2d4-0002-4a01-9a01-000000000002 -->
  > Proposta 2026-0212 como ponto de partida, conferência item a item, zero divergência nos 5 blocos migrados. Concluída 29/10 — `05_entregas/T-F3-002-migracao-conteudo-piloto.md`.

- [x] Montar a proposta do cliente com o catálogo que já existe @Fábio !10/11/2026 <!-- id:f3a1c2d4-0003-4a01-9a01-000000000003 -->
  > Montagem pelo servidor com preço vigente, snapshot congelado, condições 80/20 e validade 15 dias. **Decisão do champion:** as amostras aprovadas são CONTEÚDO da proposta. Concluída 30/10 — `05_entregas/T-F3-003-montagem-proposta.md`.

- [x] Revisar a proposta por versão e voltar para a anterior @Fábio !12/11/2026 <!-- id:f3a1c2d4-0004-4a01-9a01-000000000004 -->
  > Nova versão remontada do pedido e do catálogo do momento; rollback restaura a última revisada com recibo; histórico por linhagem. Concluída 10/11 — `05_entregas/T-F3-004-versoes-proposta.md`.

- [x] Montar o folder da Fase 3 com os blocos aprovados @Fábio !17/11/2026 <!-- id:f3a1c2d4-0005-4a01-9a01-000000000005 -->
  > Folder com blocos aprovados e catálogo vigente; bloco vazio vira pendência nomeada e bloqueia a aprovação. Concluída 23/11 — `05_entregas/T-F3-005-folder.md`.

- [x] Enviar proposta e folder com registro e recuperação @Fábio !19/11/2026 <!-- id:f3a1c2d4-0006-4a01-9a01-000000000006 -->
  > Entrega MANUAL: o vendedor baixa o PDF e envia pelo WhatsApp; o sistema registra o ato. **Decisão do champion:** a confirmação de recebimento é UM ato só (recebeu + dúvidas esclarecidas na mesma ligação). Concluída 23/11 — `05_entregas/T-F3-006-envio.md`.

- [x] Garantir permissão, auditoria e bloqueio por padrão @Fábio !24/11/2026 <!-- id:f3a1c2d4-0007-4a01-9a01-000000000007 -->
  > Alçadas na matriz, ação sem alçada NEGADA, trilha com de→para e CSV sanitizado. Concluída 23/11 — `05_entregas/T-F3-007-permissao-auditoria.md`.

- [x] Demonstrar o fluxo completo com um caso real e fechar a Fase 3 @Fábio !26/11/2026 <!-- id:f3a1c2d4-0008-4a01-9a01-000000000008 -->
  > Caso real **Maria Erotides SA — pedido 20260929-0003**. As 7 etapas aceitas uma a uma: pedido → 3 vínculos aprovados → proposta **2026-0077 = R$ 42.500,00** → revisão → envio **ENV-2026-0012** → folder FOLD-2026-0011 (111 produtos, 5 pendências de conteúdo com dono) → trilha de 14 eventos em CSV. **Correção de processo do champion:** a proposta serve para o cliente ver o VALOR; amostra vem depois da aprovação dos valores e o aceite depois do teste — lote/validade NÃO bloqueiam a proposta (v0.0.216 + migration 0043). Também corrigidos: ações de rascunho sem explicação (v0.0.217), envio duplicado por destinatário texto livre (v0.0.218) e evento de envio fora da trilha do pedido (v0.0.219 + migration 0044). Aceite: "Aprovada, com as pendências registradas". Concluída 25/11 — `05_entregas/T-F3-008-demonstracao-fluxo-completo.md`.

- [ ] Revisar as evidências e decidir o fechamento da Fase 3 @"Ramon Pádua" !30/11/2026 #aculturamento <!-- id:f3a1c2d4-0009-4a01-9a01-000000000009 -->
  > Revisão do consultor: conferir evidências e aceites das 8 tasks, confirmar as decisões herdadas da Fase 2 e decidir se a Fase 3 está fechada, com o registro do que passa para a Fase 4. Nenhuma tarefa é dada como concluída sem o registro do teste humano correspondente. Esta tarefa não abre conversa com o cliente nem com o Fábio: é decisão do consultor, uma só, que autoriza ou não o fechamento e registra o motivo por escrito. O cliente não vê esta tarefa. Depende de: todas as anteriores.
