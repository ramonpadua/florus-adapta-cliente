# T-F2-008 — Rollback transversal, fila de pendências e exportação CSV (SPEC-2-004, onda 8)

**Data:** 2026-10-23 · **Implementação:** v0.0.153–161 · **QA Skip:** completo OK

## O que foi implementado

1. **Rollback transversal (CA-2-022):** migration 0030 (alçadas rollback_produto/rollback_investimento/rollback_composicao = gestor na matriz) + hook `rollback_transversal.js` — padrão da trilha T-F2-006: rollback = nova versão com `rollback_de`, recibo na auditoria (antes→depois + motivo ≥10), vendedor negado com motivo do servidor + evento auditado, fail-closed sem alçada. Fix v0.0.158: rollback de investimento usa alçada própria (não cai em aprovar_investimento/admin). Fix v0.0.160: o motor calculo_investimento não recalcula sobre rollback (o estado restaurado permanece).
2. **Achado corrigido:** o hook trilha_preco (aprovar2) não desmarcava o vigente anterior — 9 registros "vigente" acumulados no 125231 (exatamente o que o CA-2-022 proíbe). Fix: migration 0031 (estado `substituido` + campo `substituido_por`) + hook desmarca vigentes anteriores ao aprovar. Prova: 1 vigente.
3. **Fila de pendências (CA-2-023):** rota `/fila-pendencias` (gestor/admin; vendedor 403) — agrega investimento `revisao_de_preco` + composição `nao_calculavel` + produtos `pendente_aprovacao` com DONO e MOTIVO por item; pedidos de prova filtrados fora (sem mutar dados). Hoje: 2 itens reais (20261020-0003 e 20261018-0004, dono Fabio Sacheto, motivo política I6 ausente).
4. **Exportação CSV (CA-2-024):** rota `/exportar-trilha?superficie=&dias=` (gestor/admin; vendedor 403) — CSV sanitizado SEM ator_nome (decisão do champion 23/10); mantém papel + ID + ação + antes→depois + motivo + versão + resultado. 70 eventos exportáveis hoje.
5. **UI:** seção "Pendências da calculadora — dono e motivo" + botão "Exportar CSV da trilha" no Catálogo (gestor/admin; vendedor não vê).

## Provas (TDD CP-F2-012 via API — scripts/tf2008-tdd.py + tf2008-tdd-b.py)

- **Parte B 15/15 PASS:** rollback de preço pela trilha (77,77 → 68,25 restaurado); rollback de investimento pelo GESTOR com recibo na auditoria (revisao_de_preco→calculado); rollback de composição reverte a exceção (false); rollback de produto sem versão anterior = negativa CLARA "nada a reverter" (fail-closed correto — substituição é in-place no sistema); rollback sem motivo negado; vendedor negado (RLS/alçada); fila com 2 itens reais com dono+motivo e provas fora; CSV sanitizado (sem nome próprio) com colunas de accountability; zero vigentes duplicadas (1 vigente).
- **Parte A 17/25:** as 8 falhas eram de desenho do teste (fluxo in-place), corrigidas na parte B.

## Lições

1. Substituição de produto é IN-PLACE (versao+1 no mesmo registro) — rollback de produto sem registro anterior = negativa clara; rollback de PREÇO usa a trilha T-F2-006.
2. Rollback de investimento precisa de alçada PRÓPRIA e o motor não deve recalcular sobre rollback.
3. PB v0.36: query = `e.request.url.query().get()`; filtros sem IN (OR de igualdades); POST exige `/records/`.

## Estado

- Implementação completa; **aguardando teste humano do champion** (roteiro no chat).
- Pendência de sincronização: commit local ed31146 (changelog v18 + estado) a pushar ao GitHub canônico.
