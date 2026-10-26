# SPEC-3-001 — Conteúdo aprovado: proposta e folder versionados

**Fase:** 3
**Status:** liberada
**Dono:** champion Fábio (aprovação de conteúdo); responsável por produtos/preços quando aplicável; vendedor como consumidor
**Origem no escopo:** Fase 3 — "Proposta, folder, conteúdo aprovado, versão e envio" (visão do projeto); padrões RQ-004 (conteúdo sem fonte nunca visível) e RQ-011 (retenção, padrão Fase 1)
**Degrau da solução:** conteúdo versionado no sistema Skip existente da Florus (mesma base das Fases 1–2), sem presumir ferramenta de design ou CMS externo; o conteúdo real (texto dos blocos) é insumo do champion dentro das tasks.

## Contexto e decisões fechadas

- **Estado atual:** proposta e folder vivem em documentos avulsos (Word/PDF) fora do sistema, sem versão, fonte ou aprovação registradas; o vendedor monta/envia manualmente e não há rastreio do que foi enviado.
- **Estado desejado:** blocos de conteúdo da proposta e do folder como documentos versionados no sistema, cada bloco com fonte, vigência, responsável e estado de aprovação; nada entra em proposta/folder sem aprovação registrada.
- **Decisões já fechadas:** padrão de governança idêntico ao catálogo da Fase 2 (RLS por papel, trilha de versão, exclusão bloqueada — retenção); conteúdo sem fonte/vigência/aprovação gera pendência nomeada e nunca é visível ao vendedor (padrão RN-2.002/CA-2-003); a Cotação e os campos travados do pedido não são alterados (travas de 26/08 e 16/09 mantidas).
- **Insumos embutidos (não travam o início):** estrutura de blocos da proposta (capa, entendimento, escopo, investimento, condições) e do folder (linhas, produtos, alegações), donos e fontes de cada bloco — o champion entrega dentro da task T-F3-001.

## Resultado observável

O champion cadastra/aprova os blocos de conteúdo da proposta e do folder com fonte e vigência; blocos incompletos aparecem como pendências nomeadas; o vendedor consulta blocos aprovados; toda alteração cria nova versão com trilha (anterior marcada "Substituído pelo conteúdo X") e rollback auditado.

## Limites e dependências

- **Inclui:** coleção `conteudos` com RLS, trilha de versão e exclusão bloqueada; tela de cadastro/consulta; estados `rascunho`, `pendente_aprovacao`, `aprovado`, `substituido`; pendências nomeadas; reconciliação da migração do conteúdo real; rollback de versão.
- **Fora de escopo:** gerar PDF/layout visual da proposta ou folder (SPEC-3-002/003 cuidam do documento montado); enviar proposta/folder (SPEC-3-004); conteúdo técnico-regulatório novo (alegações sem fonte viram pendência); alterar Cotação ou campos travados do pedido.
- **Entradas e pré-condições:** catálogo aprovado da Fase 2 disponível; papéis da Fase 1 reutilizados; decisão de estrutura de blocos pelo champion (insumo embutido).
- **Saídas/artefatos:** coleção `conteudos`; inventário e relatório de reconciliação; pendências por bloco.
- **Dependências e responsáveis:** champion decide estrutura e donos; responsável migra e aprova; SPEC-3-002/003 consomem blocos `aprovado`.
- **Atores e permissões mínimas:** vendedor consulta blocos aprovados; gestor/admin cadastra e aprova; RLS server-side idêntica ao padrão da Fase 1.
- **Superfícies afetadas:** nova coleção `conteudos` + hooks de validação/auditoria; nenhuma alteração nas coleções das Fases 1–2 além de leitura.
- **Risco e plano B:** conteúdo real divergente ou incompleto trava a migração; plano B é manter a operação manual atual e registrar pendências nomeadas até aprovação.
- **Rollback:** desativar blocos (ficam `rascunho`), preservar histórico, nada é apagado.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Champion → estrutura de blocos | Decisão registrada na task | Blocos da proposta e do folder, donos, fontes | Consultor/champion | Registro único | Pendência explícita se não decidida |
| Responsável → cadastro de conteúdo | Formulário de bloco | `conteudo_id`, tipo (proposta/folder), bloco, título, corpo, fonte, vigência, status, responsável, versão, timestamps | Gestor/admin (RLS) | Idempotência por `conteudo_id` | Rascunho preservado; pendência por campo ausente |
| Conteúdo → SPEC-3-002/003 | Blocos `aprovado` somente | Contrato de leitura por `conteudo_id` + versão | Vendedor leitura; hooks validam status | Leitura pura | Bloco não aprovado nunca entra em documento |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-3.001 | Campo mínimo ausente (título, corpo ou fonte) | Bloco fica `pendente_aprovacao` com pendência nomeada; nunca visível ao vendedor | Responsável completa depois sem recadastrar | RQ-004 |
| RN-3.002 | Fonte não declarada ou vigência vencida | Pendência explícita; última versão aprovada mantida | Reconciliação registrada resolve | RQ-004; AC-003 |
| RN-3.003 | Bloco duplicado (mesmo tipo+bloco) | Bloquear segunda versão vigente; sinalizar duplicidade | Substituição com trilha `substituido_por` | AC-010 |
| RN-3.004 | Tentativa de exclusão | Exclusão bloqueada; bloco vai para `substituido` com trilha | — | RQ-011 (retenção) |

## Fluxo e regras

1. Champion registra a estrutura de blocos e donos (insumo embutido na task).
2. Responsável migra o conteúdo real bloco a bloco com fonte e vigência.
3. Sistema valida campos mínimos, gera `conteudo_id`, estados e pendências nomeadas.
4. Aprovação registra ator, data, versão e motivo; bloco aprovado fica visível ao vendedor.
5. Reconciliação da migração item a item contra as fontes declaradas; divergência vira pendência.
6. Expansão para novos blocos somente após aceite do champion.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Blocos migrados com fonte e vigência | Aprovados visíveis ao vendedor; pendências nomeadas nos incompletos | Pendência não bloqueia o cadastro |
| Limite | Bloco vencido ou sem fonte | Pendência explícita; versão anterior aprovada segue válida | Reconciliação resolve; nada usado em silêncio |
| Falha | Duplicidade de bloco ou queda no cadastro | Rascunho preservado; nenhuma segunda versão vigente | Retomada sem redigitar; trilha de substituição |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; padrões RLS/auditoria da Fase 1 e catálogo da Fase 2.
2. **Alterar somente:** coleção `conteudos`, hooks de validação/auditoria, tela de cadastro/consulta.
3. **Não alterar:** coleções das Fases 1–2 (exceto leitura), Cotação, campos travados do pedido.
4. **Executar nesta ordem:** estrutura de blocos → modelar coleção → migração do conteúdo real → validações e pendências → aprovação → reconciliação → demonstração.
5. **Parar e pedir validação quando:** o champion não entregar a estrutura de blocos na task; qualquer erro exigir acesso externo não autorizado.
6. **Estado válido ao parar:** conteúdo parcial preservado, operação das Fases 1–2 intacta, pendências nomeadas visíveis.

## Checklist de execução

- [ ] Estrutura de blocos e donos registrada pelo champion.
- [ ] Coleção `conteudos` com RLS, trilha de versão e exclusão bloqueada.
- [ ] Cadastro com validação de campos mínimos e pendências nomeadas.
- [ ] Aprovação com ator, data, versão e motivo; vendedor vê somente aprovados.
- [ ] Migração reconciliada item a item com relatório.
- [ ] Duplicidade, vigência vencida e fonte divergente exercitadas.

## Critérios de aceite

- [ ] **CA-3-001:** responsável cadastra os blocos com fonte, vigência e responsável; sistema gera `conteudo_id` único com estado.
- [ ] **CA-3-002:** bloco com campo mínimo ausente fica `pendente_aprovacao` com pendência nomeada e nunca visível ao vendedor.
- [ ] **CA-3-003:** bloco sem fonte ou vencido gera pendência explícita e mantém a última versão aprovada válida.
- [ ] **CA-3-004:** divergência entre fontes abre reconciliação registrada; nenhuma versão é escolhida em silêncio.
- [ ] **CA-3-005:** duplicidade de bloco é bloqueada; substituição preserva histórico com `substituido_por`.
- [ ] **CA-3-006:** migração reconciliada com relatório item a item e aceite do champion antes de expandir.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Consultar conteúdo hoje | Perguntar ao vendedor onde está o texto aprovado da proposta e do folder e qual a versão | Não existe versão/fonte/aprovação registradas no sistema | Registro na task |
| GREEN | CP-F3-001 | Cadastrar blocos com campos mínimos e aprovar um deles | `conteudo_id` único, estados corretos, vendedor vê somente aprovados | Exportação + capturas |
| REGRESSÃO | CP-F3-002/003 | Bloco sem fonte; duplicidade de bloco | Pendência nomeada; segunda versão vigente bloqueada | Relatório de cenários |

## Tasks vinculadas

- T-F3-001 (onda 1)
- T-F3-002 (onda 2)
