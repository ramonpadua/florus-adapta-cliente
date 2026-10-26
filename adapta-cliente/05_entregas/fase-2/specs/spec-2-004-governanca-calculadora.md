# SPEC-2-004 — Governança da calculadora: auditoria, exceções e rollback

**Fase:** 2  
**Status:** liberada  
**Dono:** administrador da plataforma e gestor comercial; champion Fábio como aprovador  
**Origem no escopo:** RQ-011, AC-005, AC-011, Fase 2 — estados, auditoria, exceção e reversão da calculadora  
**Degrau da solução:** extensão do padrão de RLS/auditoria/rollback já provado na Fase 1 (SPEC-1-003, T-F1-007..009) para as superfícies novas da Fase 2.

## Contexto e decisões fechadas

- **Estado atual:** a Fase 1 entregou papéis (lead/vendedor/gestor/admin), RLS em pedidos, auditoria server-side append-only, rollback de versão e trilha de decisão — todos aprovados pelo champion até 29/09.
- **Estado desejado:** toda superfície nova da Fase 2 (catálogo, composição, investimento, preço) herda o mesmo padrão: ação sensível registra usuário, data, estado anterior/novo, versão, motivo e aprovação; vendedor não aprova o que exige alçada; contingência não cria duas versões vigentes; rollback restaura sem apagar.
- **Decisões já fechadas:** auditoria append-only server-side (padrão T-F1-007); exclusão bloqueada — reversão sempre por trilha (T-F1-009); negativa com motivo do servidor (T-F1-008); RLS provada por `totalItems` entre papéis (EV-F1-07); estados da calculadora: `calculado`, `nao_calculavel`, `revisao_necessaria`, `revisao_de_preco`, `excecao`, `aprovado`.
- **Bloqueios declarados como insumos embutidos:** matriz nominal de alçadas da Fase 2 (quem aprova preço, exceção e migração) — champion entrega na task T-F2-007; nomes por papel seguem com o champion (call de setup quando houver).

## Resultado observável

Um auditor (gestor/admin) consulta a trilha da calculadora e vê cada ação sensível das SPECs 2-001..003 com ator, papel, antes→depois, motivo, versão e resultado; uma tentativa de vendedor de aprovar preço/exceção é negada com motivo do servidor e auditada; um rollback de qualquer objeto da Fase 2 restaura a última versão aprovada com histórico íntegro e zero versões vigentes duplicadas.

## Limites e dependências

- **Inclui:** extensão da auditoria para catálogo/composição/investimento/preço; matriz de alçadas aplicada server-side; prova negativa de vendedor; rollback transversal; fila de pendências nomeadas com dono.
- **Fora de escopo:** política jurídica/privacidade formal (responsável da Florus); novos papéis além dos da Fase 1 sem decisão do champion; logs com dado pessoal desnecessário.
- **Entradas e pré-condições:** matriz de alçadas (insumo na task); coleções da Fase 2 existentes; padrão de auditoria da Fase 1 ativo.
- **Saídas/artefatos:** eventos de auditoria por superfície; exportação CSV da trilha; relatório de prova negativa; recibo de rollback.
- **Dependências e responsáveis:** SPECs 2-001..003; administrador da plataforma executa; champion aprova a matriz.
- **Atores e permissões mínimas:** vendedor nunca aprova preço/exceção/migração; gestor e admin conforme alçada; auditoria legível por gestor/admin.
- **Superfícies afetadas:** hooks de auditoria das coleções novas; tela de trilha existente (extensão); fila de pendências.
- **Risco e plano B:** matriz de alçadas ausente; plano B é negar por padrão (fail-closed) com pendência nomeada — nada é aprovado em silêncio.
- **Rollback ou reversão:** o próprio objeto da SPEC; desativar superfície nova preserva dados e mantém a Fase 1 íntegra.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Ações das SPECs 2-001..003 → auditoria | Hooks server-side | Ator, papel, ação, objeto, antes→depois, versão, motivo, resultado | Gestor/admin leem; sistema escreve | Append-only; idempotência por evento | Falha de log não aprova a ação |
| Matriz de alçadas → hooks | Cadastro aprovado pelo champion | Ação → papel mínimo | Server-side somente | Leitura pura | Sem matriz → negar por padrão |
| Auditoria → exportação | Trilha append-only | CSV por período/superfície | Gestor/admin | Exportação idempotente | Exportação sanitizada (sem dado pessoal além do necessário) |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-2.017 | Ação sensível da Fase 2 (preço, exceção, migração, rollback) | Evento de auditoria com ator, papel, antes→depois, motivo, versão, resultado | Falha de log → ação não conclui | RQ-011 |
| RN-2.018 | Vendedor tenta aprovar preço/exceção/migração | Negativa com motivo do servidor; evento auditado | — | RQ-011; padrão T-F1-008 |
| RN-2.019 | Matriz de alçadas ausente para uma ação | Negar por padrão (fail-closed) com pendência nomeada | Champion cadastra → ação liberada | RQ-011 |
| RN-2.020 | Rollback de objeto da Fase 2 | Nova versão restaurando a última aprovada; zero vigentes duplicadas | Trilha preserva a versão revertida | RQ-011; T-F1-009 |
| RN-2.021 | Pendência da calculadora | Fila com dono e motivo; sem SLA inventado | SLA definido pelo champion quando houver | RQ-012 (padrão baseline) |

## Fluxo e regras

1. Hook intercepta ação sensível; verifica papel contra a matriz de alçadas.
2. Sem alçada → negativa com motivo do servidor + evento auditado.
3. Com alçada → executa + evento append-only com antes→depois.
4. Rollback gera nova versão com `rollback_de` e recibo.
5. Pendências da calculadora ficam na fila com dono e motivo.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Gestor aprova exceção com motivo | Ação executada + evento completo na trilha | Falha de log → ação não conclui |
| Limite | Matriz sem alçada definida para a ação | Negado por padrão com pendência nomeada | Champion define → reexecutar |
| Falha | Vendedor tenta aprovar; rollback em objeto com histórico | Negativa auditada; rollback com trilha íntegra | Auditoria exporta a negativa |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; SPEC-1-003 (padrão de RLS/auditoria/rollback); RN-2.017..021.
2. **Alterar somente:** hooks de auditoria das coleções novas, aplicação da matriz de alçadas, fila de pendências, exportação da trilha.
3. **Não alterar:** coleções/auditoria da Fase 1 (extensão somente), papéis existentes, Cotação.
4. **Executar nesta ordem:** registrar matriz de alçadas (insumo) → estender hooks → provas negativas → rollback transversal → exportação → demonstração.
5. **Parar e pedir validação quando:** a matriz de alçadas não chegar com o champion na task (embutir pergunta, não travar); qualquer negativa exigir mudança de papel não decidida.
6. **Estado válido ao parar:** fail-closed ativo (nada aprovado sem alçada); Fase 1 intacta; pendências nomeadas.

## Checklist de execução

- [ ] Matriz de alçadas da Fase 2 registrada pelo champion.
- [ ] Hooks de auditoria cobrindo catálogo, composição, investimento e preço.
- [ ] Prova negativa: vendedor negado com motivo do servidor e evento auditado.
- [ ] Fail-closed comprovado para ação sem alçada definida.
- [ ] Rollback transversal com recibo e zero vigentes duplicadas.
- [ ] Exportação CSV da trilha sanitizada demonstrada.

## Critérios de aceite

- [ ] **CA-2-019:** toda ação sensível da Fase 2 gera evento de auditoria completo (ator, papel, antes→depois, motivo, versão, resultado).
- [ ] **CA-2-020:** vendedor não consegue aprovar preço, exceção ou migração; negativa com motivo do servidor e auditada.
- [ ] **CA-2-021:** ação sem alçada definida é negada por padrão com pendência nomeada (fail-closed).
- [ ] **CA-2-022:** rollback de qualquer objeto da Fase 2 restaura a última versão aprovada com histórico íntegro.
- [ ] **CA-2-023:** fila de pendências da calculadora exibe dono e motivo por item.
- [ ] **CA-2-024:** trilha exportável em CSV sem dado pessoal desnecessário.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Consultar trilha hoje | Tentar auditar quem alterou um preço e por quê | Sem trilha para as superfícies novas — registrar a lacuna | Roteiro/captura |
| GREEN | Executar ações com CP-F2-010 | Gestor aprova exceção; auditor exporta trilha | Evento completo; CSV sanitizado | Exportação + capturas |
| REFACTOR/REGRESSÃO | Repetir com CP-F2-011/012 | Vendedor tenta aprovar; ação sem alçada; rollback | Negativas auditadas; fail-closed; recibo de rollback | Relatório de cenários |

**Dados/fixtures:** CP-F2-010: exceção aprovada por gestor com motivo; CP-F2-011: vendedor tentando aprovar; CP-F2-012: rollback de preço migrado.

**Caminhos de erro obrigatórios:** vendedor sem alçada, ação sem matriz, falha de log, rollback duplo, exportação com dado pessoal.

**Evidência exigida:** eventos de auditoria por superfície, CSV exportado, relatório de provas negativas e aceite humano do gestor/champion.

## Handoff e operação

- **Como demonstrar:** aprovar exceção como gestor, tentar como vendedor (negativa), executar rollback e exportar a trilha.
- **Como operar depois:** gestor/admin revisam pendências e trilha; champion mantém a matriz de alçadas.
- **Como monitorar:** negativas por papel; pendências abertas; rollbacks; cobertura de auditoria.
- **Pendência conhecida:** matriz de alçadas é insumo do champion na task; sem ela, fail-closed mantém tudo seguro sem travar o fluxo de cálculo.

## Tasks vinculadas

| ID | Onda | Task | Critério coberto | Recorte da prova | Predecessoras | Status |
|---|---|---|---|---|---|---|
| `T-F2-007` | 7 | Estender auditoria, alçadas e fail-closed às superfícies da Fase 2 | CA-2-019, CA-2-020, CA-2-021 | TDD GREEN com CP-F2-010/011 | `T-F2-005` | Pendente |
| `T-F2-008` | 8 | Demonstrar rollback transversal, fila de pendências e exportação da trilha | CA-2-022, CA-2-023, CA-2-024 | TDD REGRESSÃO com CP-F2-012 + handoff | `T-F2-007` | Pendente |

## Emendas

Nenhuma emenda registrada.
