# SPEC-3-003 — Folder com blocos aprovados e versão

**Fase:** 3
**Status:** liberada
**Dono:** champion Fábio (conteúdo); vendedor como consumidor; gestor/admin (alçadas)
**Origem no escopo:** Fase 3 — "Proposta, folder, conteúdo aprovado, versão e envio"; RQ-004; padrões de governança da Fase 2
**Degrau da solução:** folder montado dentro do sistema Skip existente a partir dos blocos de conteúdo aprovado; sem ferramenta de design externa presumida — o formato final é insumo do champion na task.

## Contexto e decisões fechadas

- **Estado atual:** folder é documento avulso (Word/PDF) sem versão, fonte ou aprovação registradas; alegações e produtos podem divergir do catálogo aprovado sem rastreio.
- **Estado desejado:** folder montado no sistema a partir de blocos `aprovado` (linhas, produtos, alegações), com versão registrada e trilha; conteúdo vencido gera pendência, nunca entra em silêncio.
- **Decisões já fechadas:** somente blocos com fonte e vigência válidas entram no folder (padrão RN-3.002); versão do folder registrada com trilha; exclusão bloqueada (retenção); Cotação e campos travados não são alterados.
- **Insumos embutidos (não travam o início):** layout/estrutura do folder e regra de vigência por bloco — o champion entrega dentro da task T-F3-005.

## Resultado observável

O gestor gera o folder a partir dos blocos aprovados: linhas, produtos e alegações com fonte e vigência válidas; conteúdo vencido aparece como pendência nomeada e não entra; o folder tem versão com trilha e rollback auditado; o vendedor consulta a versão vigente.

## Limites e dependências

- **Inclui:** montagem do folder a partir de `conteudos` aprovados; estados e trilha de versão; pendências nomeadas por conteúdo vencido/ausente; rollback com recibo.
- **Fora de escopo:** envio do folder (SPEC-3-004); proposta (SPEC-3-002); conteúdo novo sem fonte (vira pendência); alterar Cotação ou campos travados.
- **Entradas e pré-condições:** blocos aprovados (SPEC-3-001); catálogo aprovado (Fase 2) para produtos do folder.
- **Saídas/artefatos:** coleção `folders` com trilha; folder montado; relatório de pendências de conteúdo.
- **Dependências e responsáveis:** champion decide layout e vigências; gestor/admin monta e aprova; SPEC-3-004 consome folder `revisada`.
- **Atores e permissões mínimas:** vendedor consulta versão vigente; gestor/admin monta/aprova; RLS server-side padrão.
- **Superfícies afetadas:** nova coleção `folders` + hooks; leitura de `conteudos` e `produtos`.
- **Risco e plano B:** conteúdo vencido em massa trava o folder; plano B é publicar a última versão vigente e registrar pendências nomeadas.
- **Rollback:** desativar o folder novo (fica `rascunho`), preservar histórico, nada é apagado.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Blocos → folder | Coleção `conteudos` (leitura, `aprovado`) | Corpo por bloco + vigência | RLS Fase 3 | Leitura pura | Bloco vencido → pendência nomeada, não entra |
| Catálogo → folder | Coleção `produtos` (leitura, `aprovado`) | Produtos/linhas vigentes | RLS Fase 2 | Leitura pura | Produto não aprovado nunca entra |
| Folder → SPEC-3-004 | Estado `revisada` somente | `folder_id` + versão | RLS Fase 3 | Escrita única completa | Rascunho nunca envia |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-3.009 | Bloco vencido ou sem fonte | Pendência nomeada; bloco não entra no folder | Reconciliação registrada resolve | RQ-004 |
| RN-3.010 | Montagem do folder | Nasce `rascunho`; envio bloqueado até revisão/aprovação | — | Padrão Fase 1 |
| RN-3.011 | Edição do folder | Nova versão com trilha; anterior preservada | — | Padrão Fase 1 |
| RN-3.012 | Rollback | Restaura última versão aprovada com recibo auditado | — | Padrão T-F2-008 |

## Fluxo e regras

1. Gestor gera o folder a partir dos blocos aprovados e produtos vigentes.
2. Sistema valida vigências; conteúdo vencido vira pendência nomeada e não entra.
3. Revisão/aprovação registra ator, data, versão e motivo.
4. Folder `revisada` fica pronto para o envio (SPEC-3-004).
5. Rollback disponível com recibo auditado.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Blocos aprovados com vigência válida | Folder montado completo | Pendência por bloco ausente |
| Limite | Bloco vencido | Pendência nomeada; bloco não entra; última versão vigente segue válida | Reconciliação resolve |
| Falha | Queda na montagem | Rascunho preservado; retomada sem redigitar | Tentativa preservada |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; padrões da Fase 2 (governança de conteúdo).
2. **Alterar somente:** coleção `folders`, hooks, tela de montagem/consulta.
3. **Não alterar:** coleções das Fases 1–2 (exceto leitura), Cotação, campos travados.
4. **Executar nesta ordem:** modelar coleção → montagem → validação de vigências → revisão/versão → rollback → demonstração.
5. **Parar e pedir validação quando:** o champion não entregar o layout do folder na task; vigência em massa vencida.
6. **Estado válido ao parar:** rascunhos preservados, operação das Fases 1–2 intacta.

## Checklist de execução

- [ ] Coleção `folders` com RLS e trilha de versão.
- [ ] Montagem a partir de blocos aprovados + produtos vigentes.
- [ ] Pendências nomeadas by conteúdo vencido/ausente.
- [ ] Revisão/aprovação antes de enviar.
- [ ] Rollback com recibo auditado.

## Critérios de aceite

- [ ] **CA-3-012:** folder montado somente com blocos aprovados e produtos vigentes.
- [ ] **CA-3-013:** bloco vencido gera pendência nomeada e não entra; nada entra em silêncio.
- [ ] **CA-3-014:** folder nasce `rascunho` e não envia antes da aprovação.
- [ ] **CA-3-015:** edição cria nova versão com trilha; anterior preservada.
- [ ] **CA-3-016:** rollback restaura a última versão aprovada com recibo auditado.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Montar folder hoje | Perguntar como o folder é produzido e versionado hoje | Manual, sem versão/fonte | Registro na task |
| GREEN | CP-F3-007 | Gerar folder com blocos aprovados | Montagem completa; vencidos fora com pendência | Exportação + capturas |
| REGRESSÃO | CP-F3-008 | Bloco vencido + edição | Pendência nomeada; trilha correta | Relatório de cenários |

## Tasks vinculadas

- T-F3-005 (onda 5)

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

**Nesta SPEC (T-F3-005):** o folder é da **Fase 3** (não da Fase 4) e é montado a partir dos **blocos e
das fontes que já existem** — antes de perguntar, procure o material já enviado pelo champion e peça
apenas o que faltar (linhas e produtos, alegações permitidas e a fonte aprovada de cada uma). O
material do PDF Proposta 2026-0212 já migrado não é pedido de novo. As decisões RN-3.009 a RN-3.012 e
os critérios CA-3-012 a CA-3-016 continuam valendo sem alteração.
