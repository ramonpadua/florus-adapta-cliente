# STATUS — Projeto Florus Brasil — Processo Comercial

> **Atualizado em:** 2026-11-10 · **Por:** consultoria Adapta

**Repo do cliente:** https://github.com/fnavaar/florus-adapta-cliente

## Onde estamos

- **Fase atual:** 3 — Proposta, folder, conteúdo aprovado, versão e envio · liberada em 26/10/2026
- **Fase 2 fechada formalmente em 26/10/2026:** 8/8 tasks concluídas e aprovadas pelo champion (09/10 a 26/10; T-F2-008 aceite "Deu certo" com revalidação 18/18 PASS). Revisão do consultor (Ramon Pádua, responsável pela consultoria) executada no repositório: evidências e aceites conferidos; decisões D1/D2 do handoff da Fase 1 já aprovadas pelo champion (02/10). Arquivo em `05_entregas/fase-2/` com manifesto de fechamento e check assinado. Gate de transição liberado pelo consultor em 26/10.
- **Concluída em 10/11:** **T-F3-004** (revisão, versão e rollback da proposta) — implementada em 02/11 na v0.0.188 e **concluída na v0.0.189** após duas rodadas de teste humano (a 1ª reprovou 3 pontos de tela, todos corrigidos). Próxima elegível: **T-F3-005** (folder), aguardando autorização do champion. T-F3-003 concluída em 30/10. T-F3-001 (28/10) e T-F3-002 (29/10) também concluídas. Fase 3 tem 9 tasks (T-F3-001 a T-F3-008 nas ondas 1–8 + revisão do consultor, sem número — convenção da Fase 2) e 4 SPECs (SPEC-3-001 a SPEC-3-004).
- **Pendências herdadas da Fase 2:** política de faturamento mínimo (pode ser criada depois; comparação segue em `revisao_de_preco` até lá) e exceção de piso POR PEDIDO (entra com a política I6; estado `excecao` já previsto no bloco Investimento). Exceção da trilha segue como alteração com alçada do admin.
- **Tasks:** **4/9 concluídas (44%)** — T-F3-001 (28/10), T-F3-002 (29/10), T-F3-003 (30/10) e T-F3-004 (10/11). Estrutura de conteúdo da proposta: **8 blocos** (6 originais + matérias-primas + procedimentos, decisão de 29/10); folder com 5 blocos. 5 blocos da proposta migrados e aprovados com o texto real da Proposta 2026-0212 — os 3 restantes (entendimento, matérias-primas, procedimentos) seguem sem conteúdo aprovado e aparecem como pendência nomeada na proposta. Proposta montada dentro do sistema com snapshot congelado, condições 80/20, validade 15 dias e **amostras aprovadas** (tabela no documento, pré-preenchida e editável). **T-F3-004** implementada em 02/11 (v0.0.188): nova versão remontada do pedido e do catálogo do momento, versão anterior preservada como substituída, rollback restaurando a última versão revisada com recibo; alçadas na matriz (vendedor dono + gestor/admin para versão e revisão; gestor/admin para rollback); **concluída em 10/11** após correção de tela da 1ª rodada (histórico por linhagem, seletor com todas as versões, modo consulta na substituída). Evidência: TDD **32/32 PASS** + QA OK na v0.0.189 + aceite do champion.
- Executar uma task por vez com prova e teste humano do champion antes de avançar.

## Travas ativas

| Trava | Desde | Quem resolve | Ação em curso |
|---|---|---|---|
| Precondições específicas de canal, CRM, papéis, eventos, fontes e métricas | 2026-08-20 | Responsáveis indicados nas SPECs | Confirmar antes da execução de cada task; parar sem inventar regra ou acesso |
| Sistema travado até "Necessidade de investimento para esse projeto" | 2026-08-26 | Fábio (champion) | Não alterar Dados do Cliente, Endereço, Contato, Avaliação do Pedido e Resumo de Investimento sem ordem direta |
| Lógica da Cotação congelada | 2026-09-16 | Fábio (champion) | Não alterar filtro por categoria, bloqueio por ambiguidade, alertas e cálculo do custo unitário sem ordem direta |

## Nota de ajuste documental — 2026-10-30

- Ajuste **somente documental**, autorizado por Ramon (consultor): as tasks abertas da Fase 3 (T-F3-003
  a T-F3-008 e a revisão do consultor) foram reescritas em linguagem leiga no `04_fase-atual/fase.md`
  (commit `176210e`) e os SPECs 3-002, 3-003 e 3-004 receberam a emenda **E-F3-20261030-01**, com o
  mesmo contrato de interação válido só para as tasks abertas. `AGENTS.md` e `CLAUDE.md` receberam o
  protocolo equivalente.
- **Progresso NÃO é afetado por essa nota**: atualizado em 30/10 por conclusão real da T-F3-003
  (aceite do champion, TDD 56/56 PASS, critérios CA-3-007/008/010 21/21 PASS) → **3/9 (33%)**.

## Últimas entregas concluídas

| Task | Data | Entrega | Evidência |
|---|---|---|---|
| T-F3-004 | 10/11/2026 | Revisão, versão e rollback da proposta: nova versão remontada do pedido e catálogo do momento, anterior preservada como substituída, rollback à última versão revisada com recibo, histórico por linhagem e modo consulta na versão substituída | TDD 32/32 PASS + QA OK na v0.0.189 (`05_entregas/T-F3-004-versoes-proposta.md`) |
| T-F3-003 | 30/10/2026 | Proposta montada a partir do pedido, catálogo aprovado e motor de investimento: snapshot congelado, condições 80/20, validade 15 dias, amostras aprovadas pré-preenchidas e editáveis na tabela do documento | TDD 56/56 PASS + critérios 21/21 PASS (`05_entregas/T-F3-003-montagem-proposta.md`) |
| T-F3-002 | 29/10/2026 | Conteúdo piloto migrado e aprovado com conferência item a item | TDD 22/22 PASS (`05_entregas/T-F3-002-migracao-conteudo-piloto.md`) |
| T-F3-001 | 28/10/2026 | Coleção `conteudos` com RLS, trilha de versão, conferência e exclusão bloqueada | TDD 32/32 PASS (`05_entregas/T-F3-001-conteudo-aprovado.md`) |
