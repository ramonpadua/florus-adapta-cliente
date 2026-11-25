# SPEC-4-001 — Follow-up com próxima ação, SLA e fila de oportunidades

**Fase:** 4
**Status:** liberada
**Dono:** vendedor (age e registra); gestor/admin (fila, SLA e alçadas); champion Fábio (regras de follow-up)
**Origem no escopo:** Fase 4 — "follow-up após envio e fila de oportunidades sem atividade; SLA configurável, lembrete, adiamento com motivo, encerramento e reabertura"
**Degrau da solução:** follow-up dentro do sistema Skip existente, lendo o envio registrado da Fase 3; o follow-up organiza e registra — não envia mensagem, não negocia, não desqualifica e não fecha sozinho.

## Contexto e decisões fechadas

- **Estado atual:** o envio da proposta/folder é registrado com ator, versão e destinatário (Fase 3, coleção `envios`), mas o que vem depois não existe no sistema — sem próxima ação, sem prazo, sem fila; proposta esfriada depende de memória do vendedor.
- **Estado desejado:** toda proposta enviada nasce com próxima ação (o quê, quem, até quando); fila de oportunidades enviadas visível ao gestor; SLA configurável destaca quem passou do prazo; lembrete ao dono; adiamento com motivo auditado; encerramento com motivo; reabertura com histórico preservado.
- **Decisões já fechadas:** follow-up não envia mensagem nem negocia (escopo Fase 4); auditoria server-side e fail-closed (padrão T-F2-007/T-F3-007); pendência nomeada com dono (padrão do projeto); nada é apagado — encerramento é estado, não exclusão.
- **Insumos embutidos (não travam o início):** SLA padrão em dias e visibilidade da fila (só gestor/admin ou time todo) — o champion entrega na task T-F4-001; canal do lembrete e quem pode adiar/reabrir — na task T-F4-002.

## Resultado observável

Proposta enviada ganha próxima ação automaticamente; o gestor abre a fila e vê as oportunidades enviadas, quem está parado e quem passou do SLA; adiar exige motivo e fica na trilha; encerrar exige motivo; reabrir preserva o histórico.

## Limites e dependências

- **Inclui:** próxima ação nascida do envio; fila de oportunidades; SLA configurável; lembrete; adiamento com motivo; encerramento e reaberação; auditoria de cada transição.
- **Fora de escopo:** envio de mensagem automática, negociação, desqualificação ou fechamento autônomo (escopo); integração com CRM externo (dependência condicional); alterar coleções das Fases 1–3 (exceto leitura).
- **Entradas e pré-condições:** envio registrado (SPEC-3-004); papéis da Fase 1; matriz de alçadas.
- **Saídas/artefatos:** coleção de follow-up/próxima ação; tela da fila; relatório de cenários.
- **Dependências e responsáveis:** champion define SLA e visibilidade; vendedor age e registra; gestor/admin acompanha.
- **Atores e permissões mínimas:** vendedor age nas próprias oportunidades; gestor/admin vê tudo e configura SLA; sem alçada definida → negado (fail-closed).
- **Superfícies afetadas:** nova coleção de follow-up + hooks; leitura de `envios`, `propostas`, `pedidos`.
- **Risco e plano B:** SLA mal calibrado gera ruído; plano B é fila ordenada por idade sem alerta até o champion calibrar com dados reais.
- **Rollback:** transição de estado é revertível com trilha; envio e proposta (Fase 3) permanecem intactos.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Envio → follow-up | Coleção `envios` | `envio_id`, proposta, versão, destinatário | RLS Fase 3 | 1 follow-up por envio (idempotente) | Envio sem follow-up vira pendência visível |
| Follow-up → trilha | Auditoria server-side | Ator, papel, antes→depois, motivo | Server-side hooks | Append-only | Nada é apagado |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-4.001 | Follow-up tenta enviar mensagem/negociar/fechar | Negado — follow-up só organiza e registra | — | Escopo Fase 4 |
| RN-4.002 | Oportunidade sem próxima ação | Pendência nomeada com dono; fila destaca | — | Padrão do projeto |
| RN-4.003 | SLA vencido | Destaque na fila + lembrete ao dono | Adiamento com motivo reprograma | Escopo Fase 4 |
| RN-4.004 | Adiar/encerrar sem motivo | Negado com motivo do servidor; evento auditado | — | Padrão T-F3-006 |
| RN-4.005 | Reabrir encerrado | Histórico preservado; trilha da reabertura | — | Escopo Fase 4 |

## Fluxo e regras

1. Envio registrado (Fase 3) dispara a criação da próxima ação e a entrada na fila.
2. Sistema calcula idade e SLA; atraso destaca e lembra o dono.
3. Vendedor adia (motivo obrigatório), encerra (motivo) ou registra o resultado da ação.
4. Gestor/admin acompanha a fila e exporta o estado.
5. Reabertura preserva histórico e registra ator.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Proposta enviada | Próxima ação criada; oportunidade na fila | — |
| Limite | SLA vencido | Destaque + lembrete ao dono | Adiamento com motivo reprograma |
| Falha | Follow-up órfão (envio sem próxima ação) | Pendência nomeada com dono | Criação manual auditada |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; padrões de auditoria e fail-closed das Fases 1–3.
2. **Alterar somente:** nova coleção de follow-up, hooks, tela da fila e lembrete.
3. **Não alterar:** `envios`, `propostas`, `folders` (exceto leitura), Cotação, campos travados.
4. **Executar nesta ordem:** SLA e visibilidade (insumo) → modelar coleção → próxima ação no envio → fila e destaque → lembrete/adiamento/encerramento/reabertura → demonstração.
5. **Parar e pedir validação quando:** o champion não entregar SLA/visibilidade; qualquer necessidade de mensagem automática ou integração externa (gate do consultor).
6. **Estado válido ao parar:** envios intactos, fila legível, pendências nomeadas.

## Checklist de execução

- [ ] SLA padrão e visibilidade da fila definidos pelo champion.
- [ ] Próxima ação criada no registro do envio (idempotente por envio).
- [ ] Fila de oportunidades com idade e destaque de atraso.
- [ ] Lembrete ao dono; adiamento e encerramento com motivo auditado; reabertura com histórico.
- [ ] Prova negativa: follow-up não envia mensagem, não negocia, não fecha.

## Critérios de aceite

- [ ] **CA-4-001:** envio de proposta cria próxima ação e a oportunidade aparece na fila.
- [ ] **CA-4-002:** oportunidade sem atividade além do SLA fica destacada e o dono é lembrado.
- [ ] **CA-4-003:** adiar e encerrar exigem motivo e ficam na trilha (prova negativa sem motivo).
- [ ] **CA-4-004:** reabertura preserva o histórico inteiro com ator e data.
- [ ] **CA-4-005:** papel sem alçada é negado por padrão (fail-closed).

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Proposta enviada hoje | Perguntar como o pós-envio é acompanhado hoje | Sem próxima ação nem fila; memória/caderno | Registro na task |
| GREEN | CP-F4-001/002 | Enviar proposta; abrir a fila | Próxima ação criada; oportunidade na fila com idade | Relatório de cenários |
| REGRESSÃO | CP-F4-003/004/005 | Vencer SLA; adiar sem motivo; reabrir | Destaque + lembrete; negativa auditada; histórico preservado | Relatório de cenários |

## Tasks vinculadas

- T-F4-001 (onda 1)
- T-F4-002 (onda 2)
