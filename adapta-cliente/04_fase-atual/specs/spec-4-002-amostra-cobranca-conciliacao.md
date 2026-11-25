# SPEC-4-002 — Amostra, cobrança observada e conciliação com o Financeiro

**Fase:** 4
**Status:** liberada
**Dono:** vendedor (registra aceite e cobrança); gestor/admin (conciliação); Financeiro (confirmação — dependência condicional); champion Fábio (valor e regra da cobrança)
**Origem no escopo:** Fase 4 — "aceite para amostra e registro da cobrança observada de R$ 300,00; pagamento, pendência, crédito/reembolso e conciliação com Financeiro"
**Degrau da solução:** aceite e dinheiro da amostra como estados registrados no Skip, lidos da proposta e do follow-up da Fase 3/4; o sistema REGISTRA e o Financeiro confere — integração com sistema do Financeiro é dependência condicional, não se presume.

## Contexto e decisões fechadas

- **Estado atual:** a tabela de amostras aprovadas existe na proposta (Fase 3, pré-preenchida e editável), mas o aceite do teste pela cliente e a cobrança vivem fora do sistema; lote/validade são alerta, não bloqueio (correção de processo da T-F3-008: proposta → aprovação dos valores → amostra → teste → aceite).
- **Estado desejado:** aceite da amostra registrado (quem aprovou, quando, o quê); cobrança observada registrada com valor e regra definidos pelo champion; estados do dinheiro — observada, confirmada, pendência com dono e prazo, crédito, reembolso; conciliação com o Financeiro com divergência virando pendência nomeada, nunca ajuste silencioso.
- **Decisões já fechadas:** amostra só avança com aceite e registro financeiro (escopo); rollback preserva aceite e pagamento já registrados (escopo); cobrança duplicada negada (padrão de idempotência T-F1-005); divergência é pendência com dono (padrão do projeto); número de dinheiro nunca é inventado nem copiado de escopo sem confirmação do champion.
- **Insumos embutidos (não travam o início):** valor e regra da cobrança (o R$ 300,00 do escopo precisa ser confirmado — por amostra ou por pedido?), quem registra e em que momento — champion entrega na task T-F4-003; responsável do Financeiro e método de conferência — na task T-F4-004.

## Resultado observável

A amostra testada pela cliente é aceita no sistema com quem aprovou e quando; a cobrança observada é registrada com o valor confirmado pelo champion; pagamento confirmado, pendência, crédito e reembolso têm estados com dono; o Financeiro confere e divergência vira pendência nomeada — sem duplicidade e sem ajuste silencioso.

## Limites e dependências

- **Inclui:** aceite da amostra; cobrança observada; estados do dinheiro; pendência com dono e prazo; crédito/reembolso com motivo; conciliação declarada com o Financeiro.
- **Fora de escopo:** integração automática com sistema do Financeiro (dependência condicional — gate do consultor); emissão de nota/fatura; alterar a tabela de amostras da proposta (Fase 3, exceto leitura); fabricação e expedição (escopo).
- **Entradas e pré-condições:** proposta enviada e follow-up existente (SPEC-4-001); tabela de amostras da proposta (Fase 3); matriz de alçadas.
- **Saídas/artefatos:** coleção de aceite/cobrança; tela de conciliação; relatório de cenários.
- **Dependências e responsáveis:** champion confirma valor e regra; Financeiro confirma responsável e método (condicional); vendedor registra; gestor/admin concilia.
- **Atores e permissões mínimas:** vendedor registra nas próprias oportunidades; gestor/admin concilia e exporta; Financeiro (papel novo, se confirmado) confirma pagamento — sem alçada definida → negado (fail-closed).
- **Superfícies afetadas:** nova coleção de aceite/cobrança + hooks; leitura de `propostas`, `envios`, follow-up.
- **Risco e plano B:** Financeiro não confirmar acesso/método → sistema registra e a conferência continua fora, com pendência nomeada para o responsável.
- **Rollback:** transição revertível com trilha; aceite e pagamento registrados são preservados (escopo).

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Proposta → amostra | Tabela de amostras (Fase 3) | Item, lote, validade (alerta) | RLS Fase 3 | Leitura | Amostra sem item → pendência |
| Aceite → registro | Nova coleção | Quem aprovou, quando, o quê | Server-side hooks | 1 aceite por amostra | Aceite duplicado negado |
| Cobrança → Financeiro | Coleção de cobrança | Valor, regra, estado, dono | Gestor/admin | 1 cobrança por amostra/pedido (regra do champion) | Divergência → pendência nomeada |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-4.006 | Amostra sem aceite | Não avança para cobrança/produção | — | Escopo Fase 4 |
| RN-4.007 | Cobrança duplicada | Negada com motivo do servidor; evento auditado | — | Padrão T-F1-005 |
| RN-4.008 | Valor divergente | Pendência nomeada com dono e prazo; nunca ajuste silencioso | — | Padrão do projeto |
| RN-4.009 | Crédito/reembolso | Exige motivo; trilha preserva o pagamento original | — | Escopo Fase 4 |
| RN-4.010 | Valor da cobrança | Confirmado pelo champion na task; escopo não é fonte automática | — | Regra do projeto (número não se inventa) |

## Fluxo e regras

1. Cliente aprova os valores (proposta, Fase 3) e testa a amostra.
2. Vendedor registra o aceite com quem aprovou e quando.
3. Vendedor registra a cobrança observada com o valor confirmado pelo champion.
4. Pagamento confirmado pelo responsável do Financeiro (papel condicional) ou pendência nomeada.
5. Divergência vira pendência com dono; crédito/reembolso com motivo; trilha exporta tudo.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Amostra testada e aprovada | Aceite registrado; cobrança observada com valor | — |
| Limite | Mesma cobrança registrada 2× | Segunda negada com motivo; evento auditado | `marcar_duplicado` se já existir registro legado |
| Falha | Valor divergente | Pendência com dono e prazo | Conciliação manual visível |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; padrões de idempotência (T-F1-005), pendência nomeada e fail-closed (T-F2-007).
2. **Alterar somente:** nova coleção de aceite/cobrança, hooks, tela de conciliação.
3. **Não alterar:** tabela de amostras da proposta (exceto leitura), `envios`, Cotação, campos travados.
4. **Executar nesta ordem:** valor e regra (insumo) → modelar coleção → aceite → cobrança → estados do dinheiro → conciliação → demonstração.
5. **Parar e pedir validação quando:** o champion não confirmar valor/regra; o Financeiro não confirmar responsável/método; qualquer integração externa (gate do consultor).
6. **Estado válido ao parar:** aceites preservados, cobranças sem duplicidade, pendências nomeadas.

## Checklist de execução

- [ ] Valor e regra da cobrança confirmados pelo champion.
- [ ] Aceite da amostra com quem aprovou, quando e o quê.
- [ ] Cobrança observada com valor, regra e estado; duplicada negada (prova negativa).
- [ ] Estados do dinheiro com dono; crédito/reembolso com motivo.
- [ ] Conciliação com divergência virando pendência nomeada.

## Critérios de aceite

- [ ] **CA-4-006:** aceite de amostra permite registrar cobrança, pagamento ou pendência sem duplicidade.
- [ ] **CA-4-007:** cobrança duplicada é negada com motivo e auditada (prova negativa).
- [ ] **CA-4-008:** divergência de valor vira pendência com dono e prazo, sem ajuste silencioso.
- [ ] **CA-4-009:** crédito/reembolso exige motivo e preserva o registro original.
- [ ] **CA-4-010:** papel sem alçada é negado por padrão (fail-closed).

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Amostra testada hoje | Perguntar como aceite e cobrança são registrados hoje | Fora do sistema; sem rastro | Registro na task |
| GREEN | CP-F4-006/007 | Aceitar amostra; registrar cobrança | Aceite com quem/quando; cobrança com valor | Relatório de cenários |
| REGRESSÃO | CP-F4-008/009/010 | Cobrança 2×; valor divergente; crédito | Negativa auditada; pendência com dono; motivo preservando original | Relatório de cenários |

## Tasks vinculadas

- T-F4-003 (onda 3)
- T-F4-004 (onda 4)
