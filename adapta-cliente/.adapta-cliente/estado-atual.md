# Estado atual — Adapta Cliente

- task_id: T-F4-004 (pagamento, pendência e conciliação com o Financeiro — SPEC-4-002, onda 4)
- champion: Fábio
- spec: 04_fase-atual/specs/spec-4-002-amostra-cobranca-conciliacao.md
- etapa: aguardando_autorizacao
- autorizacao_implementacao: ausente para a T-F4-004 (a de 26/11/2026 valeu para a T-F4-003)
- teste_humano: nao_aplicavel (T-F4-004 não implementada)
- verificacao_automatica: pendente (T-F4-004 não implementada). Baseline da análise: 74 cobranças no banco — 65 observada, 9 pendente (valor 0), 0 confirmada/creditada/reembolsada; única cobrança real COB-2026-0031 (Maria, 2026-0077, R$ 300, observada); nenhum registro de pagamento existe; `cobrancas` já tem estado/dono_pendencia/prazo_em/motivo/reembolso_*; 27 alçadas, NENHUMA de dinheiro; sem papel "financeiro" em users; 0 de 120 propostas com condicoes.credito > 0.
- aprendizado: T-F4-003 — (1) coleção criada por migration SEM `created`/`updated` derruba qualquer sort por esses campos, e o catch vazio esconde como "lista vazia" (migration 0049); (2) `findRecordsByFilter` com `~` e com bind `{:p}` volta vazio no runtime do hook — filtrar contando e conferindo candidato; (3) `e.next()` NÃO devolve os campos gravados; (4) campo que o hook lê precisa EXISTIR na coleção (destino_pais faltava e tudo virava Brasil); (5) proposta nasce rascunho e o aceite exige revisada — a ordem do processo do champion se aplica sozinha; (6) provar em base poluída faz o teste falhar por motivo errado — cada bateria monta o próprio cenário limpo. T-F4-002 — (1) catch vazio transforma erro de leitura em "nenhum registro" — falha de leitura tem de APARECER; (2) filtro já codificado + SDK codificando de novo = 400 silencioso; (3) histórico tem de ser por OPORTUNIDADE (followup_id_ref), não por pedido — um pedido pode ter duas; (4) a Trilha é só de gestor/admin, então o vendedor precisa de rota própria de leitura com posse; (5) prova em tela cobre o caminho inteiro — eu não cliquei no Histórico na primeira prova.
- ultima_acao: análise da T-F4-004 entregue em 27/11 (artifacts/analise-T-F4-004.md). Achado principal: `cobrancas` já tem estado/dono_pendencia/prazo_em/motivo/reembolso_*, mas NÃO existe registro de pagamento nem valor pago, e nenhuma alçada de dinheiro — sem alçada o hook fail-closed negaria toda confirmação. Desenho proposto: coleção `movimentos_cobranca` (uma linha por ato: pagamento/pendencia/resolucao/credito/reembolso) + 6 alçadas por migration; NÃO criar papel "financeiro" (a tabela de precedência vendedor/gestor/admin está cravada em todos os hooks provados e recusa papel fora dela — usar lista configurada como config_cobranca.aprovadores já faz); tela no cartão da Fila, junto do bloco de amostras.
- proxima_acao: aguardar autorização do champion para implementar a T-F4-004 (4 perguntas bloqueantes: quem confirma o pagamento, como o Financeiro confere, o que acontece na divergência e onde o desconto de R$ 300 aparece)
- atualizado_em: 2026-11-27T20:52:00-03:00
- concluido_em: (T-F4-003) 2026-11-27T20:30:00-03:00

---
## Fechamento da Fase 3 (25/11)
- Revisão do consultor (Ramon Pádua): 8/8 evidências e aceites conferidos no repositório; ensaio do fluxo completo com caso real (Maria Erotides SA) aprovado etapa por etapa em 25/11; check-fase-3.md assinado com active-sha256=0e2f0042bd34b01654cb834d95a5852458c45a64069cd22991b51a0fc3e90199.
- Pendências com dono que seguem: P1 conteúdo dos 5 blocos do folder (champion/marketing/regulatório), P2 amostras reais da Maria (vendedor, no aceite), P3 idempotência de envio por texto livre (consultor + champion — vira T-F4-007), P4 20 eventos de TDD sem referência (interno), P5 3 blocos complementares da proposta (alerta).
- Fase 3 arquivada em 05_entregas/fase-3/ (fase.md, 4 SPECs com a emenda E-F3-20261030-01, README, manifesto adapta-phase-closure/v1 e check).
