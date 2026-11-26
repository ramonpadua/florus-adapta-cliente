# Estado atual — Adapta Cliente

- task_id: (nenhuma ativa) — T-F4-002 concluída em 26/11/2026
- champion: Fábio
- spec: 04_fase-atual/specs/spec-4-001-followup-proxima-acao.md
- etapa: sem_task
- autorizacao_implementacao: confirmada 26/11/2026 — "Sim" (formulário de autorização da T-F4-002)
- teste_humano: APROVADO em 26/11/2026 — roteiro de 5 passos (4 de 5 de primeira) e re-teste do Histórico corrigido: "Deu tudo certo".
- verificacao_automatica: passou — TDD 31/31 PASS (scripts/tf4002-tdd.py); QA Skip ok v0.0.229; migrations 0046 e 0047 conferidas como applied NO SCHEMA; prova em tela do Histórico em artifacts/fila-historico-corrigido.png (2 registros: 29/11→30/11 e 28/11→29/11)
- concluido_em: 2026-11-26T07:31:00-03:00
- aprendizado: (1) catch vazio transforma erro de leitura em "nenhum registro" — falha de leitura tem de APARECER; (2) filtro já codificado + SDK codificando de novo = 400 silencioso; (3) histórico tem de ser por OPORTUNIDADE (followup_id_ref), não por pedido — um pedido pode ter duas; (4) a Trilha é só de gestor/admin, então o vendedor precisa de rota própria de leitura com posse; (5) prova em tela cobre o caminho inteiro — eu não cliquei no Histórico na primeira prova.
- ultima_acao: análise da T-F4-002 concluída (26/11) — insumos fechados pelo champion: lembrete SÓ NA TELA do sistema (sem e-mail/WhatsApp) e adiar/reabrir por vendedor dono + gestor/admin. Estado real conferido: followups com updateRule null (fechada), 1 oportunidade real na fila, nenhum usuário com e-mail cadastrado além do Fábio, relay de e-mail com limite de 10/hora e preview não envia.
- proxima_acao: propor a análise da T-F4-003 (aceite de amostra e cobrança — SPEC-4-002) e aguardar autorização
- atualizado_em: 2026-11-26T07:31:00-03:00

---
## Fechamento da Fase 3 (25/11)
- Revisão do consultor (Ramon Pádua): 8/8 evidências e aceites conferidos no repositório; ensaio do fluxo completo com caso real (Maria Erotides SA) aprovado etapa por etapa em 25/11; check-fase-3.md assinado com active-sha256=0e2f0042bd34b01654cb834d95a5852458c45a64069cd22991b51a0fc3e90199.
- Pendências com dono que seguem: P1 conteúdo dos 5 blocos do folder (champion/marketing/regulatório), P2 amostras reais da Maria (vendedor, no aceite), P3 idempotência de envio por texto livre (consultor + champion — vira T-F4-007), P4 20 eventos de TDD sem referência (interno), P5 3 blocos complementares da proposta (alerta).
- Fase 3 arquivada em 05_entregas/fase-3/ (fase.md, 4 SPECs com a emenda E-F3-20261030-01, README, manifesto adapta-phase-closure/v1 e check).