# SPEC-4-003 — Handoff com pacote mínimo, aceite por área e escalonamento

**Fase:** 4
**Status:** liberada
**Dono:** gestor/admin (envia o handoff); responsável de cada área (aceita, executa e devolve); champion Fábio (pacote mínimo, prazos e escalonamento)
**Origem no escopo:** Fase 4 — "handoff com pacote mínimo, responsável, prazo, estado, aceite, retorno e escalonamento; estados para P&D/Laboratório, Regulatório, PCP e Produção, somente quando os donos confirmarem o fluxo"
**Degrau da solução:** handoff como encaminhamento registrado dentro do Skip, lido do pedido aprovado com amostra e aceite (Fases 3–4); cada área só vê o que é dela; NENHUM fluxo interno de área é inventado — sem confirmação do dono, a área fica como pendência nomeada.

## Contexto e decisões fechadas

- **Estado atual:** o encaminhamento do pedido para as áreas posteriores (P&D/Laboratório, Regulatório, PCP, Produção) acontece fora do sistema — e-mail, WhatsApp ou boca; sem pacote mínimo definido, sem aceite formal, sem prazo, sem escalonamento quando ninguém responde.
- **Estado desejado:** handoff do pedido aprovado para as áreas com pacote mínimo (o que a área precisa para começar), responsável, prazo, estado e ACEITE obrigatório antes de qualquer avanço; recusa retorna ao responsável com estado e próxima ação; atraso gera alerta e escalonamento; timeout tem tratamento explícito; reabertura com trilha; papel sem permissão não altera preço, conteúdo, aceite nem status de outra área.
- **Decisões já fechadas:** estados de cada área só entram quando os donos confirmarem o fluxo deles (escopo); RLS por área (escopo); aceite obrigatório antes do avanço (escopo); handoff recusado retorna ao responsável (escopo); auditoria server-side e fail-closed (padrão T-F2-007/T-F3-007).
- **Insumos embutidos (não travam o início):** pacote mínimo por área, responsável de cada área e prazo padrão — os donos entregam via champion na task T-F4-005 (uma área por vez, anexo no Maestro vale como fonte); para quem escala e depois de quantos dias — na task T-F4-006.

## Resultado observável

Pedido aprovado é encaminhado às áreas com pacote mínimo, responsável e prazo; a área aceita (ou recusa com motivo, retornando ao responsável); atraso alerta e escala; o papel sem permissão é barrado com motivo; a trilha do handoff inteiro é exportável.

## Limites e dependências

- **Inclui:** handoff com pacote mínimo; aceite/recusa por área; estados e prazos por área (após confirmação do dono); alerta de atraso e escalonamento; timeout explícito; reabertura com trilha; RLS por área.
- **Fora de escopo:** inventar fluxo interno de P&D, Regulatório, PCP ou Produção (pendência nomeada até o dono confirmar); produção física e logística automatizada (escopo); integração com ERP/PCP/Produção (dependência condicional — gate do consultor).
- **Entradas e pré-condições:** pedido com amostra aceita e cobrança registrada (SPEC-4-002); papéis por área (a definir com o champion); matriz de alçadas.
- **Saídas/artefatos:** coleção de handoff + hooks; tela de handoff por área; relatório de cenários.
- **Dependências e responsáveis:** champion e donos das áreas definem pacote mínimo, responsáveis e prazos; gestor/admin envia; áreas aceitam e devolvem.
- **Atores e permissões mínimas:** cada área vê e age só no que é dela (RLS); gestor/admin envia e acompanha tudo; sem alçada definida → negado (fail-closed).
- **Superfícies afetadas:** nova coleção de handoff + hooks; leitura de `pedidos`, follow-up, aceite/cobrança.
- **Risco e plano B:** dono de área não confirmar fluxo → área fica como pendência nomeada com dono, sem estado interno inventado.
- **Rollback:** handoff é encaminhamento registrado; recusa/reabertura com trilha preserva o histórico; nada é apagado.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Pedido aprovado → handoff | `pedidos` + aceite (SPEC-4-002) | Área, pacote mínimo, responsável, prazo | RLS por área | 1 handoff ativo por área+pedido | Handoff duplicado negado |
| Handoff → trilha | Auditoria server-side | Ator, papel, antes→depois, motivo | Server-side hooks | Append-only | Nada é apagado |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-4.011 | Handoff sem aceite da área | Não avança; estado visível com prazo | — | Escopo Fase 4 |
| RN-4.012 | Handoff recusado | Retorna ao responsável com estado e próxima ação | — | Escopo Fase 4 |
| RN-4.013 | Atraso do handoff | Alerta + escalonamento (definido pelo champion) | — | Escopo Fase 4 |
| RN-4.014 | Papel sem permissão de outra área | Negado com motivo do servidor (fail-closed) | Alçada cadastrada | Escopo + padrão T-F2-007 |
| RN-4.015 | Fluxo interno de área não confirmado | Área como pendência nomeada com dono; estado interno não implementado | — | Escopo Fase 4 |

## Fluxo e regras

1. Gestor/admin envia o handoff com pacote mínimo, responsável e prazo.
2. Área aceita (avança) ou recusa com motivo (retorna ao responsável).
3. Atraso alerta e escala conforme regra do champion; timeout tem tratamento explícito.
4. Reabertura com trilha; papel sem permissão barrado com motivo.
5. Trilha do handoff exportável (CSV sanitizado, padrão T-F3-007).

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Handoff enviado com pacote mínimo | Área aceita; estado e prazo visíveis | — |
| Limite | Área recusa ou não responde | Retorno ao responsável / alerta + escalonamento | Reabertura com trilha |
| Falha | Papel de outra área tenta alterar status | Negado com motivo; evento auditado | Correção pelo dono da alçada |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; padrões de RLS por papel (Fases 1–3) e fail-closed (T-F2-007).
2. **Alterar somente:** nova coleção de handoff, hooks, telas de envio/aceite por área.
3. **Não alterar:** coleções das Fases 1–3 (exceto leitura), Cotação, campos travados.
4. **Executar nesta ordem:** pacote mínimo/responsáveis/prazos (insumo, uma área por vez) → modelar coleção → envio e aceite/recusa → retorno ao responsável → atraso/escalonamento/timeout → reabertura → demonstração.
5. **Parar e pedir validação quando:** o dono de uma área não confirmar o fluxo (área fica como pendência nomeada); qualquer integração com ERP/PCP/Produção (gate do consultor).
6. **Estado válido ao parar:** handoffs com estado e dono, recusas retornadas, pendências nomeadas.

## Checklist de execução

- [ ] Pacote mínimo, responsável e prazo definidos por área (com o dono de cada área).
- [ ] Handoff com aceite obrigatório; recusa retorna ao responsável com estado e próxima ação.
- [ ] Atraso com alerta e escalonamento; timeout com tratamento explícito.
- [ ] RLS por área; papel sem permissão barrado (prova negativa).
- [ ] Área sem fluxo confirmado fica como pendência nomeada — estado interno não inventado.

## Critérios de aceite

- [ ] **CA-4-011:** handoff enviado com pacote mínimo é aceito pela área e avança com estado e prazo.
- [ ] **CA-4-012:** handoff recusado ou atrasado retorna ao responsável com estado e próxima ação.
- [ ] **CA-4-013:** papel sem permissão não altera preço, conteúdo, aceite ou status de outra área (prova negativa).
- [ ] **CA-4-014:** timeout/duplicidade é recuperado sem criar handoff duplicado.
- [ ] **CA-4-015:** área sem fluxo confirmado aparece como pendência nomeada com dono.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Encaminhamento hoje | Perguntar como o pedido chega às áreas hoje | E-mail/WhatsApp/boca; sem aceite nem prazo | Registro na task |
| GREEN | CP-F4-011/012 | Enviar handoff; área aceitar; recusar | Estado e prazo; retorno ao responsável | Relatório de cenários |
| REGRESSÃO | CP-F4-013/014/015 | Atraso; papel de outra área; área sem fluxo | Alerta + escalonamento; negativa auditada; pendência nomeada | Relatório de cenários |

## Tasks vinculadas

- T-F4-005 (onda 5)
- T-F4-006 (onda 6)
