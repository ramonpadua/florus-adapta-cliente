# STATUS — Projeto Florus Brasil — Processo Comercial

> **Atualizado em:** 2026-10-28 · **Por:** consultoria Adapta

**Repo do cliente:** https://github.com/fnavaar/florus-adapta-cliente

## Onde estamos

- **Fase atual:** 3 — Proposta, folder, conteúdo aprovado, versão e envio · liberada em 26/10/2026
- **Fase 2 fechada formalmente em 26/10/2026:** 8/8 tasks concluídas e aprovadas pelo champion (09/10 a 26/10; T-F2-008 aceite "Deu certo" com revalidação 18/18 PASS). Revisão do consultor (Ramon Pádua, responsável pela consultoria) executada no repositório: evidências e aceites conferidos; decisões D1/D2 do handoff da Fase 1 já aprovadas pelo champion (02/10). Arquivo em `05_entregas/fase-2/` com manifesto de fechamento e check assinado. Gate de transição liberado pelo consultor em 26/10.
- **Em andamento:** **T-F3-001 implementada (v0.0.165–167), aguardando teste humano do champion.** Fase 3 tem 9 tasks (T-F3-001 a T-F3-008 nas ondas 1–8 + revisão do consultor, sem número — convenção da Fase 2) e 4 SPECs (SPEC-3-001 a SPEC-3-004).
- **Pendências herdadas da Fase 2:** política de faturamento mínimo (pode ser criada depois; comparação segue em `revisao_de_preco` até lá) e exceção de piso POR PEDIDO (entra com a política I6; estado `excecao` já previsto no bloco Investimento). Exceção da trilha segue como alteração com alçada do admin.
- **Tasks:** 0/9 concluídas — T-F3-001 com TDD CP-F3-001/002/003 **32/32 PASS**, aguardando aceite do champion. Estrutura de conteúdo aprovada em 28/10: 6 blocos de proposta, 5 de folder, gestor/admin aprova todos, três fontes por bloco, vigência padrão 31/12/2026.
- Executar uma task por vez com prova e teste humano do champion antes de avançar.

## Travas ativas

| Trava | Desde | Quem resolve | Ação em curso |
|---|---|---|---|
| Precondições específicas de canal, CRM, papéis, eventos, fontes e métricas | 2026-08-20 | Responsáveis indicados nas SPECs | Confirmar antes da execução de cada task; parar sem inventar regra ou acesso |
| Sistema travado até "Necessidade de investimento para esse projeto" | 2026-08-26 | Fábio (champion) | Não alterar Dados do Cliente, Endereço, Contato, Avaliação do Pedido e Resumo de Investimento sem ordem direta |
| Lógica da Cotação congelada | 2026-09-16 | Fábio (champion) | Não alterar filtro por categoria, bloqueio por ambiguidade, alertas e cálculo do custo unitário sem ordem direta |
