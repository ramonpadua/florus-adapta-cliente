# STATUS — Projeto Florus Brasil — Processo Comercial

> **Atualizado em:** 2026-11-26 (T-F4-002 concluída, v0.0.229) · **Por:** consultoria Adapta

**Repo do cliente:** https://github.com/fnavaar/florus-adapta-cliente

## Onde estamos

- **Fase atual:** 4 — Follow-up, amostra, cobrança, aceite e handoffs · **liberada em 2026-11-25** · **2 das 9 concluídas (22%)** · próxima: **T-F4-003** (aceite de amostra e cobrança)
- **26/11/2026 — T-F4-001 CONCLUÍDA (v0.0.227):** o envio passou a gerar a **próxima ação** da proposta (o quê, quem, até quando) e a **fila de oportunidades** entrou no menu (botão *Fila*). **Insumos decididos pelo champion:** SLA padrão de **3 dias** contado do envio (configurável, troca auditada com de→para) e fila com **gestor/admin vendo tudo e vendedor vendo só as dele**. Prova: **revalidação 21/21 PASS** + TDD 18/18; migration **0045** conferida como aplicada no schema; prova em tela da fila com a Maria Erotides SA (FU-2026-0001, prazo 28/11/2026, 0 atrasadas). Aceite do champion: "Deu tudo certo". Relatório: `05_entregas/T-F4-001-proxima-acao-fila-oportunidades.md`.
- **26/11/2026 — T-F4-002 CONCLUÍDA (v0.0.228–229):** cada oportunidade da Fila ganhou **Concluir · Adiar · Encerrar · Histórico**, e o menu mostra quantas passaram do prazo. **Insumos decididos pelo champion:** lembrete **só na tela** (sem e-mail e sem WhatsApp) e adiar/reabrir para **vendedor dono + gestor/admin**; adiar pede motivo ≥10 e nova data, encerrar pede motivo e **não apaga**, reabrir preserva o histórico. **Teste humano: 4 de 5 passos aprovados; o Histórico reprovou** — ele adiou duas vezes e a tela não mostrou nada. **Causa (três erros meus em cadeia):** filtrava a Trilha pelo pedido em vez da oportunidade, codificava o filtro duas vezes (**400**) e o `catch` vazio escondia o erro como "nenhum evento". **Correção v0.0.229:** migration **0047** (`followup_id_ref`, passado preenchido só onde não há ambiguidade) + rota `GET /followup-historico` com posse — o vendedor dono passou a ver o próprio histórico sem abrir a Trilha — + falha de leitura agora **aparece na tela**. Prova: **TDD 31/31**, migrations conferidas no schema, prova em tela com os 2 registros reais (28/11→29/11 e 29/11→30/11). Aceite: **"Deu tudo certo"** (re-teste do passo corrigido). Relatório: `05_entregas/T-F4-002-lembrete-adiar-encerrar-reabrir.md`.
- **25/11/2026 — FASE 3 FECHADA FORMALMENTE + FASE 4 LIBERADA:** revisão do consultor (Ramon Pádua) concluída — 8/8 evidências e aceites do champion conferidos no repositório (T-F3-001 28/10 a T-F3-008 25/11); check com active-sha256=0e2f0042bd34b01654cb834d95a5852458c45a64069cd22991b51a0fc3e90199; Fase 3 arquivada em `05_entregas/fase-3/` com manifesto; fase.md da Fase 4 com 9 tasks e 4 SPECs publicadas; STATUS 0/9.
- **Pendências herdadas da Fase 3 (com dono, viram insumo das tasks da Fase 4):** conteúdo dos 5 blocos do folder (P1 — champion/marketing/regulatório; o folder monta e lista, mas não aprova); amostras reais da Maria (P2 — vendedor, no aceite); **idempotência de envio por texto livre (P3 — vira a T-F4-007)**; 20 eventos de TDD sem referência (P4 — interno, ambiguidade real); 3 blocos complementares da proposta (P5 — alerta).
- **Decisões confirmadas no fechamento da Fase 3:** ordem do processo comercial corrigida pelo champion (proposta → aprovação dos valores → amostra → teste → aceite; lote/validade como alerta, migration 0043); envio manual registrado (o vendedor baixa e envia pelo WhatsApp, o sistema registra); confirmação de recebimento como ato único; DataCrazy desativado quando o Skip estiver pronto.
- **Regras da Fase 4 ditas pelo escopo:** follow-up NÃO envia mensagem, não negocia, não desqualifica e não fecha sozinho; amostra só avança com aceite e registro financeiro; handoff recusado retorna ao responsável; estados de área só entram quando os donos confirmarem o fluxo; integração com CRM/Financeiro/ERP é dependência condicional (gate do consultor).
- **Pendências herdadas da Fase 2:** política de faturamento mínimo (comparação segue em `revisao_de_preco`) e exceção de piso POR PEDIDO (entra com a política I6).
- Executar uma task por vez com prova e teste humano do champion antes de avançar.

## Travas ativas

| Trava | Desde | Quem resolve | Ação em curso |
|---|---|---|---|
| Precondições específicas de canal, CRM, papéis, eventos, fontes e métricas | 2026-08-20 | Responsáveis indicados nas SPECs | Confirmar antes da execução de cada task; parar sem inventar regra ou acesso |
| Sistema travado até "Necessidade de investimento para esse projeto" | 2026-08-26 | Fábio (champion) | Não alterar Dados do Cliente, Endereço, Contato, Avaliação do Pedido e Resumo de Investimento sem ordem direta |
| Lógica da Cotação congelada | 2026-09-16 | Fábio (champion) | Não alterar filtro por categoria, bloqueio por ambiguidade, alertas e cálculo do custo unitário sem ordem direta |

## Últimas entregas concluídas

| Task | Data | Entrega | Evidência |
|---|---|---|---|
| T-F4-002 | 26/11/2026 | Lembrete só na tela; concluir, adiar, encerrar e reabrir a oportunidade com motivo ≥10, alçada, posse e histórico preservado; falha de leitura visível na tela | TDD 31/31 PASS, migrations 0046/0047 conferidas no schema, prova em tela (`05_entregas/T-F4-002-lembrete-adiar-encerrar-reabrir.md`) |
| T-F4-001 | 26/11/2026 | Próxima ação nascida do envio e fila de oportunidades com idade, prazo e destaque de atraso; SLA de 3 dias configurável e auditável; gestor vê tudo, vendedor vê as dele | Revalidação 21/21 + TDD 18/18 PASS, migration 0045 conferida no schema, prova em tela (`05_entregas/T-F4-001-proxima-acao-fila-oportunidades.md`) |
| T-F3-008 | 25/11/2026 | Demonstração do fluxo completo com caso real (Maria Erotides SA): pedido → vinculação aprovada → proposta R$ 42.500,00 → revisão → envio registrado → folder com pendências → trilha em planilha | Migrations 0043/0044 conferidas + prova negativa 400 + trilha 14 eventos (`05_entregas/T-F3-008-demonstracao-fluxo-completo.md`) |
| T-F3-007 | 23/11/2026 | Trilha de auditoria consultável com filtros e CSV sanitizado; correção de defeito real (eventos de edição sem campo nem antes/depois) | TDD 27/27 + revalidação 13/13 PASS, QA OK v0.0.207 (`05_entregas/T-F3-007-permissao-auditoria.md`) |
| T-F3-006 | 23/11/2026 | Envio registrado com confirmação de recebimento: entrega manual declarada, confirmação como ato único, idempotência, fail-closed e painel de enviadas sem confirmação | TDD 38/38 PASS, QA OK v0.0.204 (`05_entregas/T-F3-006-envio.md`) |
| T-F3-005 | 23/11/2026 | Folder montado dos blocos aprovados: coleção com RLS, versão, linhagem e retenção; montagem no servidor; pendências nomeadas com dono; revisão com motivo; nova versão; rollback com recibo; tela no menu | TDD 25/25 + rollback 21/21 + revalidação 15/15 PASS, QA OK v0.0.200 (`05_entregas/T-F3-005-folder.md`) |
| T-F3-004 | 10/11/2026 | Revisão, versão e rollback da proposta: nova versão remontada do pedido e catálogo do momento, anterior preservada como substituída, rollback à última versão revisada com recibo, histórico por linhagem e modo consulta | TDD 32/32 PASS + QA OK na v0.0.189 (`05_entregas/T-F3-004-versoes-proposta.md`) |