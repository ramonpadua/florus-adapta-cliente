# SPECs da Fase 4 — Follow-up, amostra, cobrança e handoffs

> Liberadas em 2026-11-25 pelo consultor responsável (Ramon Pádua, consultoria Adapta) no
> fechamento formal da Fase 3. Padrões herdados: envio registrado e auditoria fail-closed da
> Fase 3 (SPEC-3-004), idempotência da Fase 1 (T-F1-005), pendência nomeada com dono e
> revisão/rollback com trilha da Fase 2.

| SPEC | O que entrega | Ondas | Tasks |
|---|---|---|---|
| spec-4-001-followup-proxima-acao.md | Próxima ação nascida do envio, fila de oportunidades, SLA, lembrete, adiamento com motivo, encerramento e reabertura | 1–2 | T-F4-001, T-F4-002 |
| spec-4-002-amostra-cobranca-conciliacao.md | Aceite da amostra, cobrança observada com valor confirmado, estados do dinheiro e conciliação com o Financeiro | 3–4 | T-F4-003, T-F4-004 |
| spec-4-003-handoff-pacote-minimo.md | Handoff com pacote mínimo, aceite/recusa por área, RLS por área, atraso, escalonamento e timeout | 5–6 | T-F4-005, T-F4-006 |
| spec-4-004-idempotencia-demonstracao.md | Chave estável de destinatário (pendência P3 da Fase 3) e demonstração do ciclo pós-envio com caso real | 7–8 | T-F4-007, T-F4-008 |

**Fora desta fase:** fechamento, governança, métricas e go-live (Fase 5); integração automática com CRM, Financeiro, ERP, PCP ou Produção (dependências condicionais — gate do consultor); envio de mensagem, negociação ou fechamento autônomos (escopo). Dúvida sobre algo fora daqui vira registro no `changelog.md`, não implementação.
