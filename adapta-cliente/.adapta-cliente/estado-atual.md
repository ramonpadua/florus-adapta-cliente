# Estado atual — Adapta Cliente

- task_id: (nenhuma ativa) — T-F4-003 concluída em 27/11/2026
- champion: Fábio
- spec: 04_fase-atual/specs/spec-4-002-amostra-cobranca-conciliacao.md
- etapa: sem_task
- autorizacao_implementacao: confirmada 26/11/2026 — "Confirmo as três recomendações": vendedor marca os fatos no pedido (visita pode ser depois), R$ 300 padrão com exceção aprovada pelo gestor, só o Fábio aprova cortesia e valor
- teste_humano: APROVADO em 27/11/2026 em duas rodadas — 1ª: "Deu tudo certo, conforme você explicou", com duas correções de modelo pedidas por ele; 2ª (re-teste das correções): "Deu tudo certo".
- verificacao_automatica: passou — TDD original **31/31** (scripts/tf4003-tdd.py) + TDD da correção **18/18** (scripts/tf4003b-tdd.py), ambos em cenário de ensaio próprio e limpo; QA Skip ok v0.0.241; migrations 0048, 0049, 0050 e 0051 conferidas como applied NO SCHEMA; prova em tela em artifacts/amostras-final.png.
- concluido_em: 2026-11-27T20:30:00-03:00
- aprendizado: T-F4-003 — (1) coleção criada por migration SEM `created`/`updated` derruba qualquer sort por esses campos, e o catch vazio esconde como "lista vazia" (migration 0049); (2) `findRecordsByFilter` com `~` e com bind `{:p}` volta vazio no runtime do hook — filtrar contando e conferindo candidato; (3) `e.next()` NÃO devolve os campos gravados; (4) campo que o hook lê precisa EXISTIR na coleção (destino_pais faltava e tudo virava Brasil); (5) proposta nasce rascunho e o aceite exige revisada — a ordem do processo do champion se aplica sozinha; (6) provar em base poluída faz o teste falhar por motivo errado — cada bateria monta o próprio cenário limpo. T-F4-002 — (1) catch vazio transforma erro de leitura em "nenhum registro" — falha de leitura tem de APARECER; (2) filtro já codificado + SDK codificando de novo = 400 silencioso; (3) histórico tem de ser por OPORTUNIDADE (followup_id_ref), não por pedido — um pedido pode ter duas; (4) a Trilha é só de gestor/admin, então o vendedor precisa de rota própria de leitura com posse; (5) prova em tela cobre o caminho inteiro — eu não cliquei no Histórico na primeira prova.
- ultima_acao: T-F4-003 concluída em 27/11/2026 (v0.0.230–241), com aceite em duas rodadas. A 1ª aprovou o bloco e trouxe DUAS CORREÇÕES DE MODELO do champion: o valor da amostra é por AMOSTRA (kit), não por produto (Maria com 3 produtos = R$ 300, não R$ 900), e a cobrança NÃO depende da aprovação dos produtos. Acrescentou: frete internacional é à parte (sistema registra, não calcula) e os R$ 300 voltam como desconto no valor do projeto; na decisão seguinte, a recusa NÃO perde o reembolso (recusou a 1ª cobrada, aprovou na 2ª → os R$ 300 da primeira também são reembolsados). Implementado com migrations 0050 e 0051, TDD 31/31 + 18/18, migrations conferidas no schema.
- proxima_acao: propor a análise da T-F4-004 (pagamento, pendência e conciliação com o Financeiro — SPEC-4-002, onda 4) e aguardar autorização
- atualizado_em: 2026-11-27T20:30:00-03:00

---
## Fechamento da Fase 3 (25/11)
- Revisão do consultor (Ramon Pádua): 8/8 evidências e aceites conferidos no repositório; ensaio do fluxo completo com caso real (Maria Erotides SA) aprovado etapa por etapa em 25/11; check-fase-3.md assinado com active-sha256=0e2f0042bd34b01654cb834d95a5852458c45a64069cd22991b51a0fc3e90199.
- Pendências com dono que seguem: P1 conteúdo dos 5 blocos do folder (champion/marketing/regulatório), P2 amostras reais da Maria (vendedor, no aceite), P3 idempotência de envio por texto livre (consultor + champion — vira T-F4-007), P4 20 eventos de TDD sem referência (interno), P5 3 blocos complementares da proposta (alerta).
- Fase 3 arquivada em 05_entregas/fase-3/ (fase.md, 4 SPECs com a emenda E-F3-20261030-01, README, manifesto adapta-phase-closure/v1 e check).
