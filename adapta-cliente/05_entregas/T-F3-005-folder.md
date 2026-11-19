# T-F3-005 — Folder montado a partir dos blocos aprovados

**Versão:** v0.0.198 · **Data:** 18/11/2026 · **Etapa:** aguardando teste humano
**Autorização:** champion respondeu "sim" em 18/11/2026 21:37, à pergunta
"Autorizo começar a parte técnica da T-F3-005?" — escopo = parte TÉCNICA (coleção, versão,
aprovação, rollback, pendências nomeadas), **sem** o conteúdo dos blocos.

## 1. Estado real antes de começar (levantado em 10/11, reconfirmado em 18/11)

| Item | Estado |
|---|---|
| Blocos de folder (capa, linhas, produtos_destaque, alegacoes, contato) | existem, em rascunho e **VAZIOS** (sem texto, sem fonte) |
| Coleção `folders` | **não existia** |
| Catálogo aprovado | 107 produtos, 7 linhas |
| Material de folder enviado | nenhum |

Consequência aceita: **o folder monta, mas não aprova enquanto o conteúdo não existir.**
Isso é o comportamento correto (CA-3-013: nada entra em silêncio), não falha de montagem.

## 2. O que foi construído

- **Migration 0040** — coleção `folders`: `folder_id` único, estado (`rascunho`/`revisada`/
  `substituida`), versão, linhagem (`folder_origem`, `versao_anterior`, `substituido_por`),
  `rollback_de`, snapshot (`blocos`, `linhas`, `produtos`), `pendencias`, `conteudo_recusado`,
  revisão (`revisado_por`, `revisado_em`, `motivo`). RLS: gestor/admin monta e aprova;
  vendedor lê **somente** `revisada`. `deleteRule: null` = retenção.
- **Hook `montagem_folder.js`** — montagem **no servidor** (o cliente não manda conteúdo,
  número nem estado): numera `FOLD-ANO-####`, lê só blocos `aprovado` e não substituídos, valida
  fonte e vigência, deriva linhas e produtos do catálogo **aprovado**, registra o recusado com
  dono. Ações: `revisar` (motivo ≥10, barrada com pendência bloqueante), `nova_versao` (remonta
  das fontes, preserva a anterior), `rollback` (restaura o **snapshot congelado** da última
  revisada, com recibo na auditoria), `arquivar` (idempotente), `DELETE` sempre negado.
- **Tela `FolderPainel`** + botão "Folder" no menu: montagem, estado, pendências nomeadas com
  dono, documento com os blocos e o catálogo, ações e histórico de versões com modo consulta na
  versão substituída.

## 3. Prova

| Critério | Prova | Resultado |
|---|---|---|
| CA-3-012 | só bloco aprovado com fonte/vigência e produto aprovado entram | PASS |
| CA-3-013 | bloco vazio/vencido/sem fonte → pendência nomeada com dono, não entra | PASS (5 pendências hoje) |
| CA-3-014 | nasce rascunho; aprovação barrada com pendência; passa sem pendência | PASS |
| CA-3-015 | edição cria versão nova, anterior preservada como substituída | PASS |
| CA-3-016 | rollback restaura a última revisada com recibo | PASS (negativa + ciclo positivo) |
| Alçadas | vendedor não monta nem aprova; RLS mostra só revisada | PASS |
| Retenção | DELETE de folder negado | PASS |
| Snapshot | não editável à mão (nem no PATCH) | PASS |

`TDD 25/25 PASS` (`scripts/tf3005-tdd.py`) · `rollback 21/21 PASS`
(`scripts/tf3005-tdd-rollback.py`) · QA Skip OK (setup/static/build/integrations) na v0.0.198.

## 4. Defeito real encontrado e corrigido no caminho

O CREATE devolvia **500**. Causa: no `registrar()` eu gravei em campos que **não existem** na
coleção `auditoria` (`entidade`, `valor_anterior`) — os reais são `event_id` (obrigatório e
único), `antes`, `depois`, `ator_papel`. `set` em campo inexistente não é ignorado: lança.
Corrigido na v0.0.195 e provado pelo TDD contra a API. **Lição:** copiar a assinatura da
auditoria do hook que já funciona, em vez de supor os nomes.

## 5. Higiene de prova

- Os 5 blocos reais foram aprovados **temporariamente** com texto de prova para exercitar o
  caminho positivo; ao final voltaram a **vazio e pendente de aprovação** (o hook proíbe editar
  aprovado no lugar, então a restauração virou versão nova `FOLD-000x-v2`).
- Os 9 folders criados pelo TDD foram **arquivados** (estado `substituida` com observação),
  nenhum apagado — retenção respeitada. **Nenhum folder vigente na tela.**

## 6. Pendência com dono

| Pendência | Dono | Efeito |
|---|---|---|
| Texto e fonte dos 5 blocos de folder | Fábio | Sem isso o folder monta e mostra os 107 produtos, mas não pode ser aprovado |
