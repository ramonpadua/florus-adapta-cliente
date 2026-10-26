# SPEC-2-001 — Cadastro governado de produtos, preços e conteúdos

**Fase:** 2  
**Status:** liberada  
**Dono:** responsável por produtos/preços (champion Fábio ou delegado); Financeiro quando aplicável; vendedor como consumidor  
**Origem no escopo:** RQ-004, RQ-013, AC-003, AC-010, Fase 2 — cadastro piloto e migração gradual  
**Degrau da solução:** catálogo próprio no sistema Skip existente da Florus (mesma base da Fase 1), sem presumir ERP ou API externa; a fonte oficial é insumo do champion dentro da task.

## Contexto e decisões fechadas

- **Estado atual:** produtos, preços e conteúdos vivem em Word legado, Excel e conhecimento das pessoas; a cotação atual alterna fontes sem rastreio. Fontes: `03-Projeto/01-Escopo.md` funcionalidades 4.9 e seção 8; vídeo `13.15.35`; RQ-004.
- **Estado desejado:** um catálogo piloto com produto, linha, SKU, unidade, densidade quando aplicável, preço/kg, lote mínimo, taxas, fontes e vigências — cada item com ID, origem, status de aprovação e responsável; migração gradual de uma linha/produto piloto com inventário e reconciliação.
- **Decisões já fechadas:** migração gradual por linha/produto piloto com aceite antes de expandir (AC-010); conteúdo sem fonte, vencido ou sem aprovação aparece como pendência, nunca é usado em silêncio (RQ-004); divergência entre fontes exige reconciliação registrada, não escolha silenciosa; a Cotação existente e os campos travados do pedido ("Necessidade de investimento", dados do cliente) não são alterados por esta SPEC (EV-F1-08).
- **Bloqueios declarados como insumos embutidos:** fonte oficial (ERP × Excel controlado × cadastro governado) e responsável por produto/preço — o champion Fábio entrega a decisão dentro da task T-F2-001; periodicidade de atualização idem. Nenhum destes trava o início da execução.

## Resultado observável

Um responsável cadastra (ou importa) a linha piloto com fonte e vigência, o sistema valida campos mínimos e exibe pendências por item sem bloquear o cadastro incompleto, e o vendedor consulta o catálogo vendo somente itens aprovados — itens sem fonte/aprovação aparecem como pendência explícita. A migração piloto é reconciliada item a item contra a fonte declarada antes da expansão.

## Limites e dependências

- **Inclui:** modelo de catálogo (produto, linha, SKU, unidade, densidade, preço, lote mínimo, taxas, fonte, vigência, status, responsável); tela de cadastro; validação de campos mínimos; estados `rascunho`, `pendente_aprovacao`, `aprovado`, `substituido`; inventário e reconciliação da migração piloto; consulta pelo vendedor.
- **Fora de escopo:** migrar todo o acervo sem inventário; aprovar fórmula, alegação ou informação regulatória automaticamente; integrar ERP/API sem decisão do champion; alterar a Cotação existente; conteúdo técnico de folder (Fase 3).
- **Entradas e pré-condições:** decisão de fonte oficial e linha piloto (insumo do champion na task); ambiente Skip da Fase 1 disponível; papéis da Fase 1 (vendedor/gestor/admin) reutilizados.
- **Saídas/artefatos:** coleção `produtos` com trilha de versão; inventário de migração; relatório de reconciliação; pendências por item.
- **Dependências e responsáveis:** champion Fábio decide fonte e linha piloto; responsável por produtos/preços cadastra e aprova; SPEC-2-002 consome o catálogo aprovado.
- **Atores e permissões mínimas:** vendedor somente consulta itens aprovados; gestor/admin cadastra e aprova (nenhuma pessoa aprova a própria alteração de preço — detalhe de alçada na SPEC-2-003); RLS server-side idêntica ao padrão da Fase 1.
- **Superfícies/arquivos/configurações afetadas:** nova coleção `produtos` e hooks de validação/auditoria no Skip; nenhuma alteração nas coleções da Fase 1 além de leitura.
- **Risco e plano B:** fonte divergente ou inventário incompleto podem travar a migração; plano B é manter a versão anterior aprovada visível, registrar a pendência e continuar a operação comercial manual da Fase 1 sem interrupção.
- **Rollback ou reversão:** desativar o catálogo novo (itens ficam `rascunho`), preservar tudo que foi cadastrado, manter a operação da Fase 1 intacta; nada é apagado.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Champion → decisão de fonte | Decisão registrada no sistema (áudio/ata da task) | Fonte oficial, linha piloto, responsável, periodicidade | Consultor/champion | Registro único, sem retry | Pendência explícita se não decidida |
| Responsável → cadastro | Formulário de produto | `produto_id`, nome, linha, SKU, unidade, densidade, preço/kg, lote mínimo, taxas, fonte, vigência, status, responsável, timestamps | Gestor/admin (RLS) | Idempotência por `produto_id`; escrita única completa | Rascunho preservado; pendência por campo ausente |
| Cadastro → SPEC-2-002 | Itens `aprovado` somente | Contrato de leitura por `produto_id` + versão | Vendedor leitura; hooks validam status | Leitura pura | Item não aprovado nunca é retornado ao cálculo |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-2.001 | Campo mínimo ausente (unidade, preço ou lote mínimo) | Item fica `pendente_aprovacao` com pendência nomeada; nunca visível ao vendedor | Responsável pode completar depois sem recadastrar | RQ-004 |
| RN-2.002 | Fonte não declarada ou vigência vencida | Pendência explícita; item aprovado anterior mantém a última versão vigente | Reconciliação registrada resolve a divergência | RQ-004; AC-003 |
| RN-2.003 | Divergência entre fontes (ERP × Excel × cadastro) | Nenhuma escolha silenciosa: registrar as duas versões e abrir pendência de reconciliação | Champion decide qual prevalece, com motivo auditado | RQ-004 |
| RN-2.004 | Item duplicado (mesmo SKU/linha) | Bloquear segunda versão vigente; sinalizar duplicidade | Substituição com trilha `substituido_por` preserva histórico | AC-010 |
| RN-2.005 | Tentativa de exclusão | Exclusão bloqueada; item vai para `substituido` com trilha | — | RQ-011 (padrão Fase 1: retenção) |

## Fluxo e regras

1. Champion registra a decisão de fonte oficial e linha piloto (insumo embutido na task).
2. Responsável cadastra ou importa a linha piloto com fonte, vigência e responsável.
3. Sistema valida campos mínimos, gera `produto_id`, estados e pendências nomeadas.
4. Aprovação registra ator, data, versão e motivo; item aprovado fica visível ao vendedor.
5. Reconciliação da migração: item a item contra a fonte declarada; divergência vira pendência, não correção silenciosa.
6. Expansão para novas linhas somente após aceite do champion na reconciliação do piloto.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Linha piloto cadastrada com todos os campos e fonte | Itens aprovados visíveis ao vendedor com vigência | Pendência se qualquer campo mínimo faltar |
| Limite | Item sem densidade ou com vigência vencida | Item `pendente_aprovacao` com pendência; versão anterior aprovada segue válida | Reconciliação resolve; nada é usado em silêncio |
| Falha | Duplicidade de SKU ou queda no cadastro | Rascunho preservado; nenhuma segunda versão vigente | Retomada sem redigitar; trilha de substituição |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; `03-Projeto/02-Escopo-Definitivo.md` Fase 2; RQ-004 e RQ-013; padrões de RLS/auditoria da SPEC-1-003.
2. **Alterar somente:** coleção `produtos`, hooks de validação/auditoria, tela de cadastro e consulta do catálogo.
3. **Não alterar:** coleções da Fase 1 (exceto leitura), Cotação existente, campos travados do pedido, política de preço (SPEC-2-003).
4. **Executar nesta ordem:** registrar decisão de fonte → modelar coleção → cadastro/importação piloto → validações e pendências → aprovação → reconciliação → demonstração.
5. **Parar e pedir validação quando:** o champion não entregar a decisão de fonte/linha piloto na task (embutir pergunta, não travar); qualquer erro exigir acesso externo (ERP/API) não autorizado.
6. **Estado válido ao parar:** catálogo parcial preservado, operação da Fase 1 intacta, pendências nomeadas visíveis ao responsável.

## Checklist de execução

- [ ] Decisão de fonte oficial e linha piloto registrada pelo champion.
- [ ] Coleção `produtos` com RLS, trilha de versão e exclusão bloqueada.
- [ ] Cadastro com validação de campos mínimos e pendências nomeadas.
- [ ] Aprovação com ator, data, versão e motivo; vendedor vê somente aprovados.
- [ ] Migração piloto reconciliada item a item com relatório.
- [ ] Duplicidade, vigência vencida e fonte divergente exercitados.

## Critérios de aceite

- [ ] **CA-2-001:** responsável cadastra a linha piloto com fonte, vigência e responsável, e o sistema gera `produto_id` único com estado.
- [ ] **CA-2-002:** item com campo mínimo ausente fica `pendente_aprovacao` com pendência nomeada e nunca visível ao vendedor.
- [ ] **CA-2-003:** item sem fonte ou com vigência vencida gera pendência explícita e mantém a última versão aprovada válida.
- [ ] **CA-2-004:** divergência entre fontes abre reconciliação registrada; nenhuma versão é escolhida em silêncio.
- [ ] **CA-2-005:** duplicidade de SKU é bloqueada; substituição preserva histórico com `substituido_por`.
- [ ] **CA-2-006:** migração piloto reconciliada com relatório item a item e aceite do champion antes de expandir.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Consultar preço hoje | Perguntar ao vendedor o preço/kg e a fonte de um produto da linha piloto | Resposta depende da pessoa; sem fonte/vigência rastreável — registrar a lacuna | Roteiro/captura do processo atual |
| GREEN | Cadastrar linha piloto com massa CP-F2-001 | Cadastrar 3 produtos completos + 1 sem densidade + 1 com vigência vencida | 3 aprovados visíveis; 2 com pendências nomeadas; `produto_id` e trilha gerados | Exportação da coleção + capturas |
| REFACTOR/REGRESSÃO | Repetir com SKU duplicado, fonte divergente e edição de item aprovado | Tentar duplicar, divergir e editar | Duplicidade bloqueada; divergência em pendência; edição gera nova versão com trilha | Relatório de cenários |

**Dados/fixtures:** CP-F2-001: linha piloto com 5 produtos (3 completos, 1 sem densidade, 1 vencido); CP-F2-002: mesmo SKU cadastrado duas vezes; CP-F2-003: mesmo produto com preço divergente entre duas fontes.

**Caminhos de erro obrigatórios:** campo mínimo ausente, fonte não declarada, vigência vencida, SKU duplicado, exclusão tentada, sessão expirada no cadastro.

**Evidência exigida:** exportação da coleção `produtos`, relatório de reconciliação da migração, capturas das pendências e aceite humano do responsável/champion.

## Handoff e operação

- **Como demonstrar:** cadastrar a linha piloto ao vivo, mostrar pendências nomeadas, consultar como vendedor (somente aprovados) e exibir o relatório de reconciliação.
- **Como operar depois:** responsável por produtos/preços mantém o catálogo; pendências são revisadas na cadência definida pelo champion.
- **Como monitorar:** percentual de itens com fonte/vigência/aprovação; pendências abertas por tipo; linhas migradas.
- **Pendência conhecida:** fonte oficial e periodicidade são insumos do champion dentro da task; expansão além do piloto exige novo aceite.

## Tasks vinculadas

| ID | Onda | Task | Critério coberto | Recorte da prova | Predecessoras | Status |
|---|---|---|---|---|---|---|
| `T-F2-001` | 1 | Definir fonte oficial, linha piloto e modelar o cadastro governado | CA-2-001, CA-2-002, CA-2-003 | TDD GREEN com CP-F2-001 | — | Pendente |
| `T-F2-002` | 2 | Migrar e reconciliar a linha piloto com duplicidades e higiene | CA-2-004, CA-2-005, CA-2-006 | TDD REGRESSÃO com CP-F2-002/003 + reconciliação | `T-F2-001` | Pendente |

## Emendas

Nenhuma emenda registrada.
