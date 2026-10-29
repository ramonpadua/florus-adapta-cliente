# T-F3-002 — Migração do conteúdo piloto com conferência item a item

**Task:** T-F3-002 (SPEC-3-001, onda 2) · **Fase:** 3 · **Versões:** v0.0.172–175
**Autorização:** champion Fábio, 2026-10-29 — "Sim, mas tenha atenção nos alertas para o cliente (células destacadas em verde) e nas etapas da entrega do projeto. Note também que o cliente deve retornar a proposta assinada, com destaque para as amostras que ele aprovou."
**Fonte declarada:** Proposta Comercial Florus nº 2026-0212 (12-fev-26), 1 página — PDF anexado pelo champion em 29/10
**Prova automática:** TDD CP-F3-002/003 — **22/22 PASS** (`scripts/tf3002-tdd.py`)

## Decisões do champion nesta task

| Ponto | Decisão |
|---|---|
| Uso da proposta atual | **Ponto de partida**, não modelo final — obsoleta e com visual ruim, será reformulada |
| Conteúdo novo desejado | Propósito do projeto · por que da linha oferecida · matérias-primas de cada produto e o que elas fazem · procedimentos possíveis com os produtos |
| Estrutura da proposta | **8 blocos** — os 6 anteriores + `materias_primas` + `procedimentos` |
| Alertas ao cliente | Atenção às **células destacadas em verde** (títulos de alerta do documento) |
| Etapas da entrega | Atenção às 20 etapas com responsável de cada uma |
| Retorno assinado | O cliente devolve a proposta **assinada**, com **destaque nas amostras aprovadas** |
| Texto de teste (PROP-0019) | Substituído pela versão real (virou histórico `substituido`) |

## O que foi construído

1. **Migration 0034** — campos de conferência na coleção `conteudos`: `conferencia` (nao_conferido · conferido · divergente), `fonte_ref` (onde o texto está na fonte), `conferido_por` / `conferido_em` (ator e data gravados pelo SERVIDOR), `divergencias` (lista nomeada), `exige_assinatura` (bloco que o cliente assina no retorno). + semente dos 2 blocos novos (`materias_primas` ordem 7, `procedimentos` ordem 8), nascendo pendentes porque **não existe fonte** para eles no sistema.
2. **Hook `validacao_conteudos.js`** — aprovação passa a exigir **conferência**: bloco `nao_conferido` não é aprovado (CA-3-006); aprovação exige `fonte_ref` preenchido; divergência registrada bloqueia até a reconciliação (CA-3-004); `conferido_por`/`conferido_em` gravados pelo servidor; evento de auditoria `conferir_conteudo`.
3. **Tela Conteúdos** — campos "Onde está na fonte" e "Conferência contra a fonte" no formulário; a linha do bloco mostra o estado da conferência, quem conferiu e se exige assinatura; o botão Aprovar só aparece com conferência feita.

## Conteúdo migrado (item a item contra o PDF)

| Bloco | Conteúdo no PDF | Estado | Conferência |
|---|---|---|---|
| Capa (PROP-0022 v3) | Cabeçalho: empresa, CNPJ, endereço, nº/data/validade, vendedor, dados do cliente | aprovado | conferido |
| Escopo (PROP-0003) | **20 etapas** da entrega, com responsável (Cliente/Florus) e observação | aprovado | conferido |
| Investimento (PROP-0004) | Estrutura da tabela (produto, tamanho, R$/un, quantidade, produção, ANVISA 1× por produto, total) + **o que não está incluído** | aprovado | conferido |
| Condições (PROP-0005) | **Alertas verdes**: ATENÇÃO embalagens, DESISTÊNCIA (6 meses), PRAZO (60 dias), PAGAMENTO | aprovado | conferido |
| Próximos passos (PROP-0006) | Dados para pagamento (PIX), **AMOSTRAS APROVADAS** (ID/produto/lote), **ASSINATURA do cliente** | aprovado | conferido |
| Entendimento (PROP-0002) | — **não existe no PDF** (conteúdo novo: propósito + porquê da linha) | rascunho pendente | — |
| Matérias-primas (PROP-0023) | — **não existe fonte** (catálogo tem nome/preço/lote, não composição) | pendente | não conferido |
| Procedimentos (PROP-0021) | — **não existe fonte** | rascunho pendente | não conferido |

**Reconciliação: 5 blocos migrados, 5/5 conferem com o documento — ZERO divergências.** Os blocos com `{{...}}` são o MODELO: cada proposta preenche com os dados do pedido. **Dados pessoais do cliente de origem (CPF, telefone, e-mail, razão social Lotcare) NÃO entraram** (provado em CP-F3-002.10).

## Alertas verdes e etapas — tratamento dado

- **Alertas verdes** (títulos destacados): texto literal no bloco `condicoes`, incluindo a frase em caixa alta "JAMAIS IMPRIMA AS EMBALAGENS OU ETIQUETAS SEM A APROVAÇÃO DAS ARTES PELA FLORUS" e a regra de desistência. O bloco `investimento` carrega a lista "o que não está incluído". Nenhum alerta foi resumido ou suavizado.
- **Etapas**: as **20 etapas** preservadas na íntegra e na ordem original, com responsável e observação (CP-F3-002.7 prova a contagem).
- **Retorno assinado + amostras**: `proximos_passos` traz AMOSTRAS APROVADAS (ID/produto/lote) e o bloco de assinatura (responsável legal, CPF, data, assinatura); os 5 blocos migrados estão com `exige_assinatura = true`.

## Provas (TDD CP-F3-002/003 — 22/22 PASS)

| Prova | Resultado |
|---|---|
| 8 blocos de proposta e 5 de folder na estrutura | PASS |
| Blocos `materias_primas` e `procedimentos` criados | PASS |
| 5 blocos aprovados com o texto do PDF | PASS |
| Capa é a versão real (não o texto de teste) | PASS |
| Escopo contém as 20 etapas | PASS |
| Condições trazem os 4 alertas verdes | PASS |
| Próximos passos exigem assinatura e destacam amostras | PASS |
| Nenhum dado pessoal do cliente de origem nos blocos | PASS |
| **CA-3-004:** aprovar com divergência registrada → BLOQUEADO | PASS |
| **CA-3-006:** aprovar sem conferência → BLOQUEADO | PASS |
| Aprovar sem dizer onde está na fonte → BLOQUEADO | PASS |
| Conferido + fonte_ref + sem divergência → APROVA | PASS |
| `conferido_por` gravado pelo servidor | PASS |
| Vendedor vê somente aprovados | PASS |
| Vendedor não vê matérias-primas/procedimentos pendentes | PASS |
| Vendedor não cria conteúdo (400) | PASS |
| Exclusão bloqueada (403) | PASS |
| Auditoria `conferir_conteudo` e `aprovar_conteudo` | PASS |

## Achados reais corrigidos durante a implementação

1. **v0.0.173 — lista vazia lida como divergência.** `divergencias = []` fazia toda aprovação falhar. Causa: o JSONField chega ao hook como `[]byte` do Go; `Array.isArray()` não reconhece slice.
2. **v0.0.174 — a correção anterior ainda não bastava.** `JSON.stringify([]byte("[]"))` devolve `"[91,93]"` (números dos bytes), não `"[]"`. O hook passou a **decodificar o array de bytes** de volta para texto JSON antes de decidir. Sem isso, nenhum bloco poderia ser aprovado.

## Pendências nomeadas

- **`entendimento`** — conteúdo novo (propósito do projeto + porquê da linha); não existe no PDF. Aguarda texto do champion.
- **`materias_primas`** — não existe base de composição por produto no sistema. Aguarda Regulatório/Qualidade (decisão: conteúdo novo, não inventar).
- **`procedimentos`** — idem; aguarda Marketing/Produtos.
- **Folder (5 blocos)** — nenhum material de origem; seguem vazios e invisíveis ao vendedor.
- **Visual da proposta** — fora desta task: a reformulação visual é a T-F3-003 (montagem).

## Estado final do banco

- Vendedor vê **5 blocos** (capa, escopo, investimento, condições, próximos passos) — todos conferidos e aprovados.
- Blocos de prova do TDD: `substituido` (retenção preservada, nada apagado).
- `PROP-0019` (texto de teste) → `substituido` por `PROP-0022`.

## Gate

**Teste humano do champion pendente.** Roteiro na mensagem de entrega.
