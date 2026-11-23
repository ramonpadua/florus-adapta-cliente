# STATUS — Projeto Florus Brasil — Processo Comercial

> **Atualizado em:** 2026-11-23 (auditoria concluída, v0.0.207) · **Por:** consultoria Adapta

**Repo do cliente:** https://github.com/fnavaar/florus-adapta-cliente

## Onde estamos

- **Fase atual:** 3 — Proposta, folder, conteúdo aprovado, versão e envio · liberada em 26/10/2026
- **Fase 2 fechada formalmente em 26/10/2026:** 8/8 tasks concluídas e aprovadas pelo champion (09/10 a 26/10; T-F2-008 aceite "Deu certo" com revalidação 18/18 PASS). Revisão do consultor (Ramon Pádua) executada no repositório: evidências e aceites conferidos; decisões D1/D2 do handoff da Fase 1 já aprovadas pelo champion (02/10). Arquivo em `05_entregas/fase-2/`. Gate de transição liberado pelo consultor em 26/10.
- **23/11/2026 — T-F3-007 CONCLUÍDA (v0.0.206–207):** trilha de auditoria consultável e exportável (CSV sem nome de pessoa) e correção de defeito real: edições fora da lista gravavam evento sem campo nem de/para. O 1º teste humano reprovou a coluna "Campo" (64 de 500 linhas com `—`, histórico + criação) — eu provara o hook, não a tela; corrigido na v0.0.207 com rótulos distintos. TDD 27/27 + revalidação 13/13 PASS.
- **23/11/2026 — T-F3-006 CONCLUÍDA (v0.0.201–205):** envio registrado com confirmação de recebimento. Entrega manual (o vendedor baixa o PDF e envia pelo WhatsApp; o sistema registra), confirmação como ato único (recebeu + dúvidas esclarecidas na mesma ligação), coleção `envios` (migration 0041), hook com negativa de rascunho, fail-closed, idempotência e retenção, tela **Envios** no menu e bloco "Envios desta proposta" dentro da proposta (emenda v0.0.205, pedida no teste). Aceite do champion; TDD 38/38 PASS. **Decisão registrada:** o DataCrazy será desativado quando o Skip estiver pronto — o controle é todo do sistema.
- **23/11/2026 — T-F3-005 CONCLUÍDA (v0.0.193–200):** folder montado a partir dos blocos aprovados e do catálogo vigente, com versão, trilha, revisão com motivo, rollback com recibo, retenção e tela no menu. Aceite sobre roteiro de 8 passos; TDD 25/25 + rollback 21/21 + revalidação 15/15 PASS. **Pendência do champion:** os 5 blocos de conteúdo estão vazios — o folder monta e lista o que falta, mas não aprova.
- **18/11/2026 — varredura de integridade (v0.0.190–192):** pedido do champion para garantir funcionamento independentemente de prazo. Zero defeito crítico; 6 problemas reais corrigidos: 23 pedidos de teste arquivados (Pipe de 43 → 2 cartões), 9 números duplicados resolvidos sem apagar nada, índice UNIQUE parcial em `pedido_id` (migration 0039), proposta 2026-0019 com linhagem corrigida. Relatório `05_entregas/integridade-2026-11-18.md`.
- **Concluída em 10/11:** **T-F3-004** (revisão, versão e rollback da proposta), após duas rodadas de teste humano. T-F3-003 em 30/10; T-F3-001 (28/10) e T-F3-002 (29/10) também.
- **Pendências herdadas da Fase 2:** política de faturamento mínimo (comparação segue em `revisao_de_preco` até lá) e exceção de piso POR PEDIDO (entra com a política I6). Exceção da trilha segue como alteração com alçada do admin.
- **Tasks:** **7/9 concluídas (78%)** — T-F3-001 (28/10), T-F3-002 (29/10), T-F3-003 (30/10), T-F3-004 (10/11), T-F3-005, **T-F3-006 e T-F3-007 (23/11)**. Próxima: **T-F3-008** (demonstração do fluxo completo com caso real) — última do champion antes da revisão do consultor. Estrutura de conteúdo da proposta: **8 blocos**; folder com 5 blocos. Proposta montada no sistema com snapshot congelado, condições 80/20, validade 15 dias e **amostras aprovadas** (tabela no documento, pré-preenchida e editável).
- Executar uma task por vez com prova e teste humano do champion antes de avançar.

## Travas ativas

| Trava | Desde | Quem resolve | Ação em curso |
|---|---|---|---|
| Precondições específicas de canal, CRM, papéis, eventos, fontes e métricas | 2026-08-20 | Responsáveis indicados nas SPECs | Confirmar antes da execução de cada task; parar sem inventar regra ou acesso |
| Sistema travado até "Necessidade de investimento para esse projeto" | 2026-08-26 | Fábio (champion) | Não alterar Dados do Cliente, Endereço, Contato, Avaliação do Pedido e Resumo de Investimento sem ordem direta |
| Lógica da Cotação congelada | 2026-09-16 | Fábio (champion) | Não alterar filtro por categoria, bloqueio por ambiguidade, alertas e cálculo do custo unitário sem ordem direta |

## Nota de ajuste documental — 2026-10-30

- Ajuste **somente documental**, autorizado por Ramon (consultor): as tasks abertas da Fase 3 foram reescritas em linguagem leiga no `04_fase-atual/fase.md` (commit `176210e`) e os SPECs 3-002, 3-003 e 3-004 receberam a emenda **E-F3-20261030-01**, com o mesmo contrato de interação válido só para as tasks abertas. `AGENTS.md` e `CLAUDE.md` receberam o protocolo equivalente.

## Últimas entregas concluídas

| Task | Data | Entrega | Evidência |
|---|---|---|---|
| T-F3-007 | 23/11/2026 | Trilha de auditoria consultável com filtros e CSV sanitizado; correção de defeito real (eventos de edição sem campo nem antes/depois) | TDD 27/27 + revalidação 13/13 PASS, QA OK v0.0.207 (`05_entregas/T-F3-007-permissao-auditoria.md`) |
| T-F3-006 | 23/11/2026 | Envio registrado com confirmação de recebimento: entrega manual declarada, confirmação como ato único, idempotência, fail-closed e painel de enviadas sem confirmação | TDD 38/38 PASS, QA OK v0.0.204 (`05_entregas/T-F3-006-envio.md`) |
| T-F3-005 | 23/11/2026 | Folder montado dos blocos aprovados: coleção com RLS, versão, linhagem e retenção; montagem no servidor; pendências nomeadas com dono; revisão com motivo; nova versão; rollback com recibo; tela no menu | TDD 25/25 + rollback 21/21 + revalidação 15/15 PASS, QA OK v0.0.200 (`05_entregas/T-F3-005-folder.md`) |
| T-F3-004 | 10/11/2026 | Revisão, versão e rollback da proposta: nova versão remontada do pedido e catálogo do momento, anterior preservada como substituída, rollback à última versão revisada com recibo, histórico por linhagem e modo consulta | TDD 32/32 PASS + QA OK na v0.0.189 (`05_entregas/T-F3-004-versoes-proposta.md`) |
| T-F3-003 | 30/10/2026 | Proposta montada a partir do pedido, catálogo aprovado e motor de investimento: snapshot congelado, condições 80/20, validade 15 dias, amostras aprovadas pré-preenchidas e editáveis | TDD 56/56 PASS + critérios 21/21 PASS (`05_entregas/T-F3-003-montagem-proposta.md`) |
| T-F3-002 | 29/10/2026 | Conteúdo piloto migrado e aprovado com conferência item a item | TDD 22/22 PASS (`05_entregas/T-F3-002-migracao-conteudo-piloto.md`) |
| T-F3-001 | 28/10/2026 | Coleção `conteudos` com RLS, trilha de versão, conferência e exclusão bloqueada | TDD 32/32 PASS (`05_entregas/T-F3-001-conteudo-aprovado.md`) |
