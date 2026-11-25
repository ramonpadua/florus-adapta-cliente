# STATUS — Projeto Florus Brasil — Processo Comercial

> **Atualizado em:** 2026-11-25 (T-F3-008 concluída, v0.0.219) · **Por:** consultoria Adapta

**Repo do cliente:** https://github.com/fnavaar/florus-adapta-cliente

## Onde estamos

- **25/11/2026 — T-F3-008 CONCLUÍDA (v0.0.216–219) — Fase 3 em 8/9 (89%):** demonstração do fluxo completo com **caso real** — Maria Erotides SA, pedido **20260929-0003** ("Linha para careca"), acompanhamento só do champion. Aceite: "Aprovada, com as pendências registradas", com as sete etapas aprovadas uma a uma: pedido → 3 vínculos aprovados → proposta **2026-0077 = R$ 42.500,00** (produção R$ 41.150 + ANVISA R$ 1.350, 450 por produto uma vez) → proposta revisada → envio **ENV-2026-0012** por WhatsApp → folder **FOLD-2026-0011** com 111 produtos e 5 pendências de conteúdo nomeadas → trilha de **14 eventos** em CSV sanitizado. **Quatro defeitos reais corrigidos, todos achados executando:** (1) **ordem do processo invertida** — eu exigia lote/validade de amostra para revisar e enviar uma proposta que serve para mostrar o **valor**; o champion corrigiu (proposta → aprovação dos valores → amostra → teste → aceite) e as pendências de amostra viraram alerta (migration **0043**); (2) ações de rascunho desapareciam sem explicação quando a proposta virava revisada — **falha de roteiro minha** (roteiro velho para estado novo), com a tela passando a explicar onde está a ação válida; (3) **envio duplicado aceito** para a mesma pessoa, porque a idempotência compara destinatário **texto livre** — criada a ação `marcar_duplicado` que marca sem apagar (ENV-2026-0011 marcado, ENV-2026-0012 válido); (4) **o envio não aparecia na trilha do pedido** — evento gravado sem `pedido_id_ref`; hook corrigido e migration **0044** preencheu os antigos por evidência de instante (8 de 28; **20 intactos por ambiguidade real**, sem tocar em autor, hora ou resultado). Prova: migrations conferidas como `applied` no schema e prova negativa (motivo curto → 400; ação inexistente → 400). Relatório `05_entregas/T-F3-008-demonstracao-fluxo-completo.md`. **Pendências registradas:** conteúdo dos 5 blocos de folder, amostras reais no aceite e a correção da idempotência de envio por texto livre.
- **Fase atual:** 3 — Proposta, folder, conteúdo aprovado, versão e envio · liberada em 26/10/2026 · **8 das 9 tasks concluídas**; falta apenas a revisão do consultor
- **Fase 2 fechada formalmente em 26/10/2026:** 8/8 tasks concluídas e aprovadas pelo champion (09/10 a 26/10; T-F2-008 aceite "Deu certo" com revalidação 18/18 PASS). Revisão do consultor (Ramon Pádua) executada no repositório: evidências e aceites conferidos; decisões D1/D2 do handoff da Fase 1 já aprovadas pelo champion (02/10). Arquivo em `05_entregas/fase-2/`. Gate de transição liberado pelo consultor em 26/10.
- **23/11/2026 — T-F3-007 CONCLUÍDA (v0.0.206–207):** trilha de auditoria consultável e exportável (CSV sem nome de pessoa) e correção de defeito real: edições fora da lista gravavam evento sem campo nem de/para. O 1º teste humano reprovou a coluna "Campo" (64 de 500 linhas com `—`, histórico + criação) — eu provara o hook, não a tela; corrigido na v0.0.207 com rótulos distintos. TDD 27/27 + revalidação 13/13 PASS.
- **23/11/2026 — T-F3-006 CONCLUÍDA (v0.0.201–205):** envio registrado com confirmação de recebimento. Entrega manual (o vendedor baixa o PDF e envia pelo WhatsApp; o sistema registra), confirmação como ato único (recebeu + dúvidas esclarecidas na mesma ligação), coleção `envios` (migration 0041), hook com negativa de rascunho, fail-closed, idempotência e retenção, tela **Envios** no menu e bloco "Envios desta proposta" dentro da proposta (emenda v0.0.205, pedida no teste). Aceite do champion; TDD 38/38 PASS. **Decisão registrada:** o DataCrazy será desativado quando o Skip estiver pronto — o controle é todo do sistema.
- **23/11/2026 — T-F3-005 CONCLUÍDA (v0.0.193–200):** folder montado a partir dos blocos aprovados e do catálogo vigente, com versão, trilha, revisão com motivo, rollback com recibo, retenção e tela no menu. Aceite sobre roteiro de 8 passos; TDD 25/25 + rollback 21/21 + revalidação 15/15 PASS. **Pendência do champion:** os 5 blocos de conteúdo estão vazios — o folder monta e lista o que falta, mas não aprova.
- **18/11/2026 — varredura de integridade (v0.0.190–192):** pedido do champion para garantir funcionamento independentemente de prazo. Zero defeito crítico; 6 problemas reais corrigidos: 23 pedidos de teste arquivados (Pipe de 43 → 2 cartões), 9 números duplicados resolvidos sem apagar nada, índice UNIQUE parcial em `pedido_id` (migration 0039), proposta 2026-0019 com linhagem corrigida. Relatório `05_entregas/integridade-2026-11-18.md`.
- **Concluída em 10/11:** **T-F3-004** (revisão, versão e rollback da proposta), após duas rodadas de teste humano. T-F3-003 em 30/10; T-F3-001 (28/10) e T-F3-002 (29/10) também.
- **Pendências herdadas da Fase 2:** política de faturamento mínimo (comparação segue em `revisao_de_preco` até lá) e exceção de piso POR PEDIDO (entra com a política I6). Exceção da trilha segue como alteração com alçada do admin.
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
| T-F3-008 | 25/11/2026 | Demonstração do fluxo completo com caso real (Maria Erotides SA): pedido → vinculação aprovada → proposta R$ 42.500,00 → revisão → envio registrado → folder com pendências → trilha em planilha | Migrations 0043/0044 conferidas + prova negativa 400 + trilha 14 eventos (`05_entregas/T-F3-008-demonstracao-fluxo-completo.md`) |
| T-F3-007 | 23/11/2026 | Trilha de auditoria consultável com filtros e CSV sanitizado; correção de defeito real (eventos de edição sem campo nem antes/depois) | TDD 27/27 + revalidação 13/13 PASS, QA OK v0.0.207 (`05_entregas/T-F3-007-permissao-auditoria.md`) |
| T-F3-006 | 23/11/2026 | Envio registrado com confirmação de recebimento: entrega manual declarada, confirmação como ato único, idempotência, fail-closed e painel de enviadas sem confirmação | TDD 38/38 PASS, QA OK v0.0.204 (`05_entregas/T-F3-006-envio.md`) |
| T-F3-005 | 23/11/2026 | Folder montado dos blocos aprovados: coleção com RLS, versão, linhagem e retenção; montagem no servidor; pendências nomeadas com dono; revisão com motivo; nova versão; rollback com recibo; tela no menu | TDD 25/25 + rollback 21/21 + revalidação 15/15 PASS, QA OK v0.0.200 (`05_entregas/T-F3-005-folder.md`) |
| T-F3-004 | 10/11/2026 | Revisão, versão e rollback da proposta: nova versão remontada do pedido e catálogo do momento, anterior preservada como substituída, rollback à última versão revisada com recibo, histórico por linhagem e modo consulta | TDD 32/32 PASS + QA OK na v0.0.189 (`05_entregas/T-F3-004-versoes-proposta.md`) |
| T-F3-003 | 30/10/2026 | Proposta montada a partir do pedido, catálogo aprovado e motor de investimento: snapshot congelado, condições 80/20, validade 15 dias, amostras aprovadas pré-preenchidas e editáveis | TDD 56/56 PASS + critérios 21/21 PASS (`05_entregas/T-F3-003-montagem-proposta.md`) |
| T-F3-002 | 29/10/2026 | Conteúdo piloto migrado e aprovado com conferência item a item | TDD 22/22 PASS (`05_entregas/T-F3-002-migracao-conteudo-piloto.md`) |
| T-F3-001 | 28/10/2026 | Coleção `conteudos` com RLS, trilha de versão, conferência e exclusão bloqueada | TDD 32/32 PASS (`05_entregas/T-F3-001-conteudo-aprovado.md`) |
