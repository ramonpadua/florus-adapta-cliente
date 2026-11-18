# STATUS — Projeto Florus Brasil — Processo Comercial

> **Atualizado em:** 2026-11-18 · **Por:** consultoria Adapta

**Repo do cliente:** https://github.com/fnavaar/florus-adapta-cliente

## Onde estamos

- **18/11/2026 — varredura de integridade (v0.0.190–192):** pedido do champion para garantir funcionamento independentemente de prazo. Zero defeito crítico; 6 problemas reais corrigidos: 23 pedidos de teste arquivados (Pipe de 43 → 2 cartões), 9 números duplicados resolvidos sem apagar nada, índice UNIQUE parcial em `pedido_id` (migration 0039) impedindo repetição, proposta 2026-0019 com linhagem corrigida. Pendências com dono: remoção da conta Navaar (exige superusuário) e conteúdo dos 5 blocos de folder. Relatório `05_entregas/integridade-2026-11-18.md`.
- **Fase atual:** 3 — Proposta, folder, conteúdo aprovado, versão e envio · liberada em 26/10/2026
- **Fase 2 fechada formalmente em 26/10/2026:** 8/8 tasks concluídas e aprovadas pelo champion (09/10 a 26/10; T-F2-008 aceite "Deu certo" com revalidação 18/18 PASS). Revisão do consultor executada no repositório: evidências e aceites conferidos; arquivo em `05_entregas/fase-2/` com manifesto de fechamento e check assinado. Gate de transição liberado pelo consultor em 26/10.
- **Concluída em 10/11:** **T-F3-004** (revisão, versão e rollback da proposta) — implementada em 02/11 na v0.0.188 e concluída na v0.0.189 após duas rodadas de teste humano (a 1ª reprovou 3 pontos de tela, todos corrigidos). Próxima elegível: **T-F3-005** (folder), aguardando autorização do champion. T-F3-003 concluída em 30/10; T-F3-001 (28/10) e T-F3-002 (29/10) também concluídas. Fase 3 tem 9 tasks e 4 SPECs.
- **Pendências herdadas da Fase 2:** política de faturamento mínimo (comparação segue em `revisao_de_preco` até lá) e exceção de piso POR PEDIDO (entra com a política I6).
- **Tasks:** **4/9 concluídas (44%)** — T-F3-001 (28/10), T-F3-002 (29/10), T-F3-003 (30/10) e T-F3-004 (10/11). **T-F3-005 (folder)** está analisada e aguardando autorização: os 5 blocos de folder existem em rascunho e VAZIOS, sem coleção `folders` — a parte técnica pode ser construída sem o conteúdo, mas a montagem não. 107 produtos aprovados no catálogo e 15 domínios prontos.
- Executar uma task por vez com prova e teste humano do champion antes de avançar.

## Travas ativas

| Trava | Desde | Quem resolve | Ação em curso |
|---|---|---|---|
| Precondições específicas de canal, CRM, papéis, eventos, fontes e métricas | 2026-08-20 | Responsáveis indicados nas SPECs | Confirmar antes da execução de cada task; parar sem inventar regra ou acesso |
| Sistema travado até "Necessidade de investimento para esse projeto" | 2026-08-26 | Fábio (champion) | Não alterar Dados do Cliente, Endereço, Contato, Avaliação do Pedido e Resumo de Investimento sem ordem direta |
| Lógica da Cotação congelada | 2026-09-16 | Fábio (champion) | Não alterar filtro por categoria, bloqueio por ambiguidade, alertas e cálculo do custo unitário sem ordem direta |

## Nota de ajuste documental — 2026-10-30

- Ajuste **somente documental**, autorizado por Ramon (consultor): as tasks abertas da Fase 3 foram reescritas em linguagem leiga no `04_fase-atual/fase.md` (commit `176210e`) e os SPECs 3-002, 3-003 e 3-004 receberam a emenda **E-F3-20261030-01**. `AGENTS.md` e `CLAUDE.md` receberam o protocolo equivalente.

## Últimas entregas concluídas

| Task | Data | Entrega | Evidência |
|---|---|---|---|
| Integridade | 18/11/2026 | Varredura completa: arquivamento de 23 pedidos de teste, resolução de 9 números duplicados sem apagar nada, trava UNIQUE parcial em pedido_id, linhagem da 2026-0019 corrigida | QA OK v0.0.192 + prova em tela (Pipe 43 → 2) (`05_entregas/integridade-2026-11-18.md`) |
| T-F3-004 | 10/11/2026 | Revisão, versão e rollback da proposta: nova versão remontada do pedido e catálogo do momento, anterior preservada como substituída, rollback à última versão revisada com recibo, histórico por linhagem e modo consulta na substituída | TDD 32/32 PASS + QA OK na v0.0.189 (`05_entregas/T-F3-004-versoes-proposta.md`) |
| T-F3-003 | 30/10/2026 | Proposta montada a partir do pedido, catálogo aprovado e motor de investimento: snapshot congelado, condições 80/20, validade 15 dias, amostras aprovadas pré-preenchidas e editáveis na tabela do documento | TDD 56/56 PASS + critérios 21/21 PASS (`05_entregas/T-F3-003-montagem-proposta.md`) |
| T-F3-002 | 29/10/2026 | Conteúdo piloto migrado e aprovado com conferência item a item | TDD 22/22 PASS (`05_entregas/T-F3-002-migracao-conteudo-piloto.md`) |
| T-F3-001 | 28/10/2026 | Coleção `conteudos` com RLS, trilha de versão, conferência e exclusão bloqueada | TDD 32/32 PASS (`05_entregas/T-F3-001-conteudo-aprovado.md`) |
