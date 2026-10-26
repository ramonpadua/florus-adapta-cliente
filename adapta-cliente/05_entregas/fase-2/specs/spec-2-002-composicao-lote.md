# SPEC-2-002 — Composição por produto e cálculo de lote

**Fase:** 2  
**Status:** liberada  
**Dono:** vendedor; validação de responsável técnico/produção quando necessário  
**Origem no escopo:** RQ-005, AC-002, Fase 2 — composição por produto e cálculo de lote  
**Degrau da solução:** cálculo determinístico no Skip sobre o catálogo aprovado da SPEC-2-001; fórmulas de conversão/densidade e precedência de lote são insumos do champion/produção dentro das tasks.

## Contexto e decisões fechadas

- **Estado atual:** o vendedor monta a composição mentalmente ou em planilha, convertendo unidades e lotes de cabeça; propostas fabrilmente inviáveis passam pela revisão humana. Fontes: `01-Escopo.md` funcionalidade 4.7, automação 6.5, regras 5.1.8, 5.1.15–5.1.17; vídeo `13.15.34 (1)` 04:00–05:20.
- **Estado desejado:** cada combinação de produto e volumetria vira uma linha identificável; o sistema converte para a unidade da fábrica, compara com o lote mínimo, mostra diferença e quantidade sugerida — ou declara "não calculável" com o dado faltante nomeado.
- **Decisões já fechadas:** sem densidade, unidade ou mínimo, o resultado é `nao_calculavel` (AC-002/RQ-005); a regra de 1.000 unidades por SKU e o mínimo em kg só coexistem com precedência definida (insumo do champion); nenhuma equivalência universal entre ml/g/kg é presumida; composição inválida é preservada e nunca apresentada como viável.
- **Bloqueios declarados como insumos embutidos:** fórmulas de conversão e densidade por produto, precedência de lote (1.000 unidades × mínimo kg) — champion/técnico entregam dentro da task T-F2-003; não travam o início.

## Resultado observável

O vendedor monta um pedido com múltiplos produtos e volumetrias em linhas separadas; cada linha mostra unidade convertida, lote mínimo, diferença e quantidade sugerida com a fórmula usada; um caso sem densidade/conversão fica `nao_calculavel` com pendência nomeada, sem virar viável nem bloquear as demais linhas.

## Limites e dependências

- **Inclui:** linhas de composição vinculadas ao `pedido_id` da Fase 1; conversão de unidade com fórmula registrada; comparação com lote mínimo; quantidade sugerida; estados `calculado`, `nao_calculavel`, `revisao_necessaria`; registro de ajuste/exceção.
- **Fora de escopo:** calcular investimento/preço (SPEC-2-003); inventar densidade ou substituir decisão técnica; alterar a Cotação existente; produção/fabricação.
- **Entradas e pré-condições:** catálogo da SPEC-2-001 com itens aprovados; fórmulas de conversão e precedência registradas (insumo na task); pedido da Fase 1 disponível.
- **Saídas/artefatos:** composição por pedido com linhas identificáveis; resultado de cálculo por linha com fórmula e fonte; pendências nomeadas.
- **Dependências e responsáveis:** SPEC-2-001 (catálogo aprovado); técnico/produção valida fórmulas; SPEC-2-003 consome o resultado.
- **Atores e permissões mínimas:** vendedor monta e consulta a própria composição; gestor/admin veem tudo; RLS por pedido como na Fase 1.
- **Superfícies afetadas:** nova coleção `composicao` (ou extensão do pedido), hooks de cálculo, tela de composição dentro do fluxo do pedido.
- **Risco e plano B:** fórmula errada ou ausente gera cálculo falso; plano B é `nao_calculavel` explícito com o dado faltante e fallback manual registrado (o vendedor calcula como hoje e o sistema registra a exceção).
- **Rollback ou reversão:** desativar o cálculo automático mantendo as linhas salvas; operação manual da Fase 1 segue intacta; nada é apagado.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Pedido F1 → composição | `pedidos` (leitura) | `pedido_id`, produtos, volumetria, quantidade | Vendedor do próprio pedido; gestor/admin | Linhas vinculadas por `pedido_id`; recálculo idempotente | Sem pedido válido, não monta composição |
| Catálogo → cálculo | `produtos` aprovados (SPEC-2-001) | `produto_id`, unidade, densidade, lote mínimo, fórmula, fonte, vigência | Leitura por hook server-side | Leitura pura | Item não aprovado → linha `nao_calculavel` com pendência |
| Composição → SPEC-2-003 | Linhas `calculado` | Contrato por linha: produto, quantidade convertida, mínimo, diferença, sugerido, fórmula | Leitura autorizada | Leitura pura | Linha não calculável nunca alimenta investimento |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-2.006 | Produto adicionado com volumetria/quantidade | Linha identificável com unidade declarada; conversão usa a fórmula cadastrada | Sem fórmula → `nao_calculavel` com pendência | RQ-005 |
| RN-2.007 | Unidades incompatíveis sem densidade | `nao_calculavel`; proibido presumir equivalência ml/g/kg | Técnico cadastra densidade → recálculo | RQ-005; AC-002 |
| RN-2.008 | Quantidade abaixo do lote mínimo | Mostrar mínimo, diferença e quantidade sugerida; não bloquear | Ajuste aceito registra motivo (exceção auditada) | RQ-005 |
| RN-2.009 | Conflito 1.000 unidades/SKU × mínimo kg | Aplicar a precedência definida pelo champion e exibir qual regra foi usada | Sem precedência definida → `revisao_necessaria` | RQ-005; 5.1.15–17 |
| RN-2.010 | Cálculo inválido em uma linha | Linha `nao_calculavel`; demais linhas seguem calculadas | Composição preservada; nunca marcada viável | AC-002 |

## Fluxo e regras

1. Vendedor abre o pedido da Fase 1 e adiciona produtos/volumetrias em linhas separadas.
2. Sistema lê o catálogo aprovado e aplica conversão com a fórmula cadastrada.
3. Compara com o lote mínimo, exibe diferença e sugerido com a regra usada.
4. Dado faltante → linha `nao_calculavel` com pendência nomeada; composição preservada.
5. Ajuste abaixo do mínimo registra motivo e responsável (exceção auditada).

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Pedido avec 3 produtos e volumetrias válidas | 3 linhas calculadas com conversão, mínimo, diferença e sugerido | Falha de leitura → linha pendente, sem duplicar |
| Limite | Produto sem densidade ou fórmula | Linha `nao_calculável` nomeando o dado; demais linhas calculadas | Técnico cadastra → recálculo da linha |
| Falha | Conflito de precedência ou item não aprovado | `revisao_necessaria` com regra/pendência explícita | Champion define precedência → recálculo |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; RQ-005; regras 5.1.8 e 5.1.15–5.1.17 do escopo base; contrato de leitura da SPEC-2-001.
2. **Alterar somente:** composição, hooks de cálculo, tela de linhas do pedido.
3. **Não alterar:** catálogo (somente leitura), Cotação existente, política de preço/investimento (SPEC-2-003), coleções de auditoria da Fase 1.
4. **Executar nesta ordem:** registrar fórmulas/precedência (insumo) → modelar linhas → cálculo e conversão → mínimo/sugerido → pendências → demonstração.
5. **Parar e pedir validação quando:** fórmula/precedência não chegar com o champion na task (embutir pergunta, não travar); cálculo exigir dado externo não cadastrado.
6. **Estado válido ao parar:** composições existentes preservadas; cálculo desativável; operação manual segue.

## Checklist de execução

- [ ] Fórmulas de conversão/densidade e precedência de lote registradas pelo champion/técnico.
- [ ] Linhas de composição vinculadas ao pedido com RLS.
- [ ] Conversão com fórmula visível e fonte do catálogo.
- [ ] Mínimo, diferença e sugerido demonstrados por linha.
- [ ] `nao_calculavel` e `revisao_necessaria` exercitados sem bloquear demais linhas.
- [ ] Exceção de ajuste com motivo e responsável auditados.

## Critérios de aceite

- [ ] **CA-2-007:** pedido com múltiplos produtos calcula cada linha de forma identificável com unidade, conversão e fonte.
- [ ] **CA-2-008:** caso com densidade/unidade/conversão ausente fica `nao_calculavel` com pendência nomeada, sem virar viável.
- [ ] **CA-2-009:** resultado mostra fórmula usada, lote mínimo, diferença e quantidade sugerida.
- [ ] **CA-2-010:** conflito de precedência (1.000 un × mínimo kg) aplica a regra definida e exibe qual foi usada; sem regra, `revisao_necessaria`.
- [ ] **CA-2-011:** quantidade abaixo do mínimo gera sugestão e ajuste com motivo auditado, sem bloqueio.
- [ ] **CA-2-012:** linha inválida não contamina as demais; composição é preservada em qualquer falha.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Calcular lote hoje | Pedir ao vendedor para calcular conversão e mínimo de um pedido real de membro | Cálculo mental/planilha sem fórmula rastreável — registrar a lacuna | Roteiro/captura |
| GREEN | Montar composição com CP-F2-004 | Pedido com 3 produtos válidos + 1 sem densidade | 3 linhas `calculado` com fórmula/mínimo/sugerido; 1 `nao_calculavel` nomeada | Exportação + capturas |
| REFACTOR/REGRESSÃO | Repetir com CP-F2-005/006 | Abaixo do mínimo, conflito de precedência, item não aprovado | Sugerido + exceção auditada; regra exibida; `revisao_necessaria` | Relatório de cenários |

**Dados/fixtures:** CP-F2-004: pedido com 4 linhas (3 válidas, 1 sem densidade); CP-F2-005: linha abaixo do mínimo; CP-F2-006: produto com conflito 1.000 un × mínimo kg.

**Caminhos de erro obrigatórios:** densidade ausente, fórmula ausente, item não aprovado, abaixo do mínimo, precedência indefinida, sessão expirada.

**Evidência exigida:** exportação da composição com fórmulas, capturas por linha, relatório de exceções e aceite humano do vendedor/técnico.

## Handoff e operação

- **Como demonstrar:** montar o pedido piloto ao vivo, mostrar linha calculada e linha `nao_calculavel`, exibir a regra de precedência usada.
- **Como operar depois:** vendedor monta; técnico/produção revisa fórmulas; pendências de conversão ficam visíveis ao responsável.
- **Como monitorar:** percentual de linhas calculadas vs. `nao_calculavel`; exceções de mínimo; retrabalho de lote.
- **Pendência conhecida:** fórmulas e precedência são insumos do champion/técnico na task; sem elas, linhas ficam `nao_calculavel` sem travar o fluxo.

## Tasks vinculadas

| ID | Onda | Task | Critério coberto | Recorte da prova | Predecessoras | Status |
|---|---|---|---|---|---|---|
| `T-F2-003` | 3 | Configurar composição, conversão e cálculo de lote | CA-2-007, CA-2-008, CA-2-009 | TDD GREEN com CP-F2-004 | `T-F2-001` | Pendente |
| `T-F2-004` | 4 | Exercitar mínimo, precedência, exceções e não-calculáveis | CA-2-010, CA-2-011, CA-2-012 | TDD REGRESSÃO com CP-F2-005/006 | `T-F2-003` | Pendente |

## Emendas

Nenhuma emenda registrada.
