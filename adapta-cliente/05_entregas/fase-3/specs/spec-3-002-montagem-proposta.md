# SPEC-3-002 — Montagem da proposta a partir do pedido e do catálogo

**Fase:** 3
**Status:** liberada
**Dono:** vendedor (monta/revisa); champion Fábio (aprova conteúdo e condições); gestor/admin (alçadas)
**Origem no escopo:** Fase 3 — "Proposta, folder, conteúdo aprovado, versão e envio"; RQ-004; padrões de versionamento da Fase 1 (pedidos) e do motor de investimento da Fase 2
**Degrau da solução:** proposta montada dentro do sistema Skip existente (mesma base das Fases 1–2), consumindo pedido + catálogo + investimento; sem ferramenta de PDF externa presumida — o formato final é insumo do champion na task.

## Contexto e decisões fechadas

- **Estado atual:** proposta é montada manualmente em documento avulso a partir do pedido, da tabela de preços e do investimento calculado; sem versão, sem rastreio do que foi enviado, risco de divergência com o catálogo aprovado.
- **Estado desejado:** proposta gerada dentro do sistema a partir do pedido da Fase 1 + blocos de conteúdo aprovado + tabela de produtos do pedido com preços vigentes + investimento com componentes separados; nasce `rascunho`, só envia após revisão do vendedor e registro do ato.
- **Decisões já fechadas:** proposta nasce `rascunho` e nunca é enviável antes da revisão do vendedor; versionamento com trilha (nova versão ao editar, anterior "Substituído pela proposta X" — padrão Fase 1); rollback para a última versão aprovada com recibo auditado (padrão T-F2-008); Cotação e campos travados do pedido não são alterados.
- **Insumos embutidos (não travam o início):** layout/estrutura do PDF final e regras de validade/condições — o champion entrega dentro das tasks T-F3-003/T-F3-004.

## Resultado observável

O vendedor abre o pedido, gera a proposta em um clique: blocos aprovados preenchidos automaticamente, tabela de produtos do pedido com preços vigentes, investimento com componentes, condições e validade; edita o rascunho (nova versão com trilha), revisa e marca como pronta para envio; rollback auditado disponível.

## Limites e dependências

- **Inclui:** montagem da proposta (blocos + produtos + investimento + condições); estados `rascunho`, `revisada`, `enviada` (envio na SPEC-3-004); versionamento com trilha; rollback com recibo; fila de pendências de conteúdo da proposta.
- **Fora de escopo:** envio propriamente dito (SPEC-3-004); folder (SPEC-3-003); alterar Cotação, campos travados do pedido ou o motor de investimento da Fase 2 (consumo por leitura).
- **Entradas e pré-condições:** pedido da Fase 1 com produtos; catálogo aprovado (SPEC-2-001); investimento calculado (SPEC-2-003); blocos aprovados (SPEC-3-001).
- **Saídas/artefatos:** coleção `propostas` com trilha; proposta montada; recibo de rollback.
- **Dependências e responsáveis:** vendedor monta e revisa; gestor/admin aprova condições fora do padrão; SPEC-3-004 consome proposta `revisada`.
- **Atores e permissões mínimas:** vendedor monta/revisa as próprias propostas (RLS: só as suas); gestor/admin tudo; nenhuma pessoa aprova a própria alteração de condições (alçada na SPEC-3-004).
- **Superfícies afetadas:** nova coleção `propostas` + hooks; leitura de `pedidos`, `produtos`, `investimentos`, `conteudos`.
- **Risco e plano B:** divergência entre preço do pedido e catálogo vigente → proposta nasce com pendência nomeada (padrão `revisao_de_preco` da Fase 2), nunca divergência silenciosa.
- **Rollback:** desativar a proposta nova (fica `rascunho`), preservar histórico, nada é apagado.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Pedido → proposta | Coleção `pedidos` (leitura) | `pedido_id`, produtos, condições | RLS padrão Fase 1 | Leitura pura | Pedido sem produtos → pendência nomeada |
| Catálogo → proposta | Coleção `produtos` (leitura, `aprovado`) | Preço/kg vigente por item | RLS Fase 2 | Leitura pura | Preço divergente → pendência `revisao_de_preco` |
| Investimento → proposta | Motor da Fase 2 (leitura) | Componentes separados | RLS Fase 2 | Leitura pura | Sem cálculo → pendência nomeada |
| Blocos → proposta | Coleção `conteudos` (leitura, `aprovado`) | Corpo por bloco | RLS Fase 3 | Leitura pura | Bloco ausente → pendência nomeada |
| Proposta → SPEC-3-004 | Estado `revisada` somente | `proposta_id` + versão | RLS Fase 3 | Escrita única completa | Rascunho nunca envia |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-3.005 | Proposta criada | Nasce `rascunho`; envio bloqueado até revisão do vendedor | — | Padrão Fase 1 |
| RN-3.006 | Edição de proposta | Nova versão com trilha; anterior marcada "Substituído pela proposta X" | — | Padrão Fase 1 |
| RN-3.007 | Preço do pedido divergente do catálogo vigente | Pendência `revisao_de_preco` nomeada; proposta não envia | Resolução registrada pela trilha da Fase 2 | AC-003 |
| RN-3.008 | Rollback | Restaura última versão aprovada com recibo auditado (ator, antes→depois, motivo) | — | Padrão T-F2-008 |

## Fluxo e regras

1. Vendedor abre o pedido e gera a proposta (montagem automática dos blocos aprovados).
2. Sistema valida produtos, preços vigentes e investimento; divergência vira pendência nomeada.
3. Vendedor edita o rascunho (nova versão com trilha) e revisa.
4. Proposta `revisada` fica pronta para o envio (SPEC-3-004).
5. Rollback disponível com recibo auditado.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Pedido completo + blocos aprovados | Proposta montada com todos os componentes | Pendência nomeada por componente ausente |
| Limite | Preço divergente ou bloco vencido | Pendência `revisao_de_preco`/conteúdo; proposta não envia | Resolução registrada, não silenciosa |
| Falha | Queda na montagem | Rascunho preservado; retomada sem redigitar | Tentativa preservada (padrão T-F1-008) |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; padrões de versionamento da Fase 1 e trilha da Fase 2.
2. **Alterar somente:** coleção `propostas`, hooks, tela de montagem/revisão.
3. **Não alterar:** coleções das Fases 1–2 (exceto leitura), Cotação, campos travados.
4. **Executar nesta ordem:** modelar coleção → montagem → validações e pendências → revisão/versão → rollback → demonstração.
5. **Parar e pedir validação quando:** o champion não entregar o layout/condições na task; divergência estrutural entre pedido e catálogo.
6. **Estado válido ao parar:** rascunhos preservados, operação das Fases 1–2 intacta.

## Checklist de execução

- [ ] Coleção `propostas` com RLS e trilha de versão.
- [ ] Montagem automática a partir de pedido + catálogo + investimento + blocos.
- [ ] Pendências nomeadas por componente ausente/divergente.
- [ ] Revisão do vendedor obrigatória antes de enviar.
- [ ] Rollback com recibo auditado.

## Critérios de aceite

- [ ] **CA-3-007:** proposta gerada a partir do pedido com blocos aprovados, produtos vigentes e investimento com componentes.
- [ ] **CA-3-008:** proposta nasce `rascunho` e não envia antes da revisão do vendedor.
- [ ] **CA-3-009:** edição cria nova versão with trilha; anterior preservada como "Substituído pela proposta X".
- [ ] **CA-3-010:** divergência de preço/conteúdo gera pendência nomeada; nada diverge em silêncio.
- [ ] **CA-3-011:** rollback restaura a última versão aprovada com recibo auditado.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Montar proposta hoje | Perguntar ao vendedor como monta e envia a proposta | Manual, sem versão/rastreio | Registro na task |
| GREEN | CP-F3-004 | Gerar proposta de um pedido real | Montagem completa com componentes separados | Exportação + capturas |
| REGRESSÃO | CP-F3-005/006 | Editar (nova versão) e divergir preço | Trilha correta; pendência `revisao_de_preco`; envio bloqueado | Relatório de cenários |

## Tasks vinculadas

- T-F3-003 (onda 3)
- T-F3-004 (onda 4)

## Emenda E-F3-20261030-01 (30/10/2026) — contrato de interação das tasks abertas

**Vale só para as tasks abertas da Fase 3** (T-F3-003 a T-F3-008 e a revisão do consultor). Não altera
nada das Fases 1 e 2, não reabre task concluída e não muda critério de aceite, prazo nem responsável.
A emenda existe porque o `04_fase-atual/fase.md` foi reescrito em linguagem leiga em 30/10/2026
(commit `176210e`) e o texto da task passou a ser o contrato que vale na conversa com o champion.

**Ordem da explicação, em qualquer task aberta:** primeiro o que **já existe** (e que não deve ser
pedido de novo), depois o que **vai existir no Skip**, depois **por que é útil**. Só então a pergunta.

**Coleta no Maestro:** uma pergunta por vez, no chat do Maestro, e **só sobre o que falta** — o que já
está nos documentos, nas respostas anteriores ou no sistema não é perguntado outra vez. Anexo que o
champion mandar fica no Maestro e não é repassado por WhatsApp nem por e-mail.

**Antes de construir:** conferir a fonte de cada informação e **pedir autorização** ao champion,
mostrando o que vai ser montado; sem o "ok" nada é construído. Informação sem fonte aprovada vira
pendência com nome e dono — nunca é preenchida no silêncio, e nenhuma regra, número, benefício ou
rótulo de tela é inventado (se o rótulo não foi conferido, a ação é descrita pelo resultado).

**Prova:** roteiro humano passo a passo com os rótulos como aparecem na tela e o resultado esperado de
cada passo; uma task por vez, com o aceite do champion antes da próxima. Nenhum envio a cliente real
sem confirmação na hora.

**Nesta SPEC (T-F3-003 e T-F3-004):** a proposta piloto é **personalizada para o projeto** — o champion
informa a linha de atuação ou o tipo de cliente, o entendimento do problema, os ativos e materiais que
podem ser citados e a função de quem recebe. **Toda informação técnica só entra com fonte aprovada.**
O conteúdo já migrado do PDF Proposta 2026-0212 (alertas verdes e as 20 etapas) **não é pedido de novo
nem recriado**; a montagem parte do pedido, do catálogo com preço vigente e do motor de investimento
que já existem. As decisões RN-3.005 a RN-3.008 e os critérios CA-3-007 a CA-3-011 continuam valendo
sem alteração.
