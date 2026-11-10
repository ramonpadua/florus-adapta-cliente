# T-F3-004 — Revisão, versão e rollback da proposta

**Fase:** 3 · **SPEC:** SPEC-3-002 (onda 4) · **Versão:** v0.0.189 · **Data:** 02/11/2026 · **Concluída:** 03/11/2026
**Critérios:** CA-3-009 (edição cria versão com trilha; anterior preservada) · CA-3-011 (rollback restaura a última versão aprovada com recibo)

## O problema que existia

A proposta era um **snapshot congelado**: o sistema negava qualquer alteração no documento e a
mensagem de erro mandava para uma task que ainda não existia ("entregue na T-F3-004"). Na prática,
corrigir uma proposta depois de ela existir era impossível — só montando outra do zero, sem
vínculo com a primeira. Os campos de linhagem (`versao`, `substituido_por`, `versao_anterior`)
existiam desde a v0.0.177 e **nunca eram preenchidos**.

## Decisões do champion (formulário 02/11/2026, 20:47)

| Decisão | Resposta |
|---|---|
| Quem cria nova versão / edita | **Vendedor dono + gestor/admin** |
| Quem marca como revisada | **Vendedor dono + gestor/admin** |
| Quem volta para a última versão | **Gestor/admin** |
| O que a versão nova traz | **Remontar com o pedido e o catálogo do momento** |

## O que foi entregue

- **Migration 0037** — `proposta_origem` (raiz da linhagem), `rollback_de` (qual versão foi
  restaurada) e **três alçadas na `matriz_alcadas`** (`nova_versao_proposta` e `revisar_proposta`
  = vendedor; `rollback_proposta` = gestor), com `vendedor` incluído no select de papel mínimo.
  Fail-closed continua valendo: ação sem linha na matriz é negada.
- **Hook `montagem_proposta.js`** — `nova_versao` e `rollback` entram pelo **CREATE**: o motor de
  montagem é um só (o mesmo da T-F3-003), porque `require('./local')` não existe neste runtime.
  A prova compara as duas portas linha a linha (CP-F3-006), então uma divergência de fórmula
  aparece no TDD, não no documento do cliente.
  - **Nova versão:** remonta do pedido + catálogo do momento; herda **condições** e **amostras**
    da anterior como ponto de partida; nasce **rascunho**; exige motivo ≥ 10.
  - **Rollback:** copia o **conteúdo congelado** da última versão que passou pela revisão
    (não remonta o que já foi aprovado), em versão nova, com **recibo na auditoria** (ator,
    antes → depois, motivo). Sem versão revisada na linhagem, é negado com motivo.
  - **Substituição:** a versão que sai de cena fica `substituida` + `substituido_por`; nada é apagado.
- **Tela `PropostaPainel`** — bloco próprio **"Versões da proposta ANO-####"** (padrão de 30/10:
  ação que a pessoa precisa achar não fica na linha do título), com "Criar nova versão",
  "Voltar para a última versão revisada", campo de motivo e o **histórico da linhagem**
  (proposta · versão · estado · de onde veio · substituída por).

## Dois defeitos reais que o próprio TDD encontrou

1. **v0.0.187 — a versão revisada "desaparecia" para o rollback.** Ao gerar a versão seguinte, a
   anterior vira `substituida` e o critério "estado = revisada" deixava de reconhecê-la: rollback
   respondia "não há versão aprovada". Passou a valer **quem tem `revisado_em`** (passou pela
   revisão), mesmo substituída.
2. **v0.0.188 — duas versões vigentes na linhagem.** O rollback marcava como substituída a versão
   **de origem** (de onde copiava o conteúdo) em vez da versão **atual** (a que estava sendo
   trocada). Resultado: a linhagem ficava com duas vigentes. Corrigido com `substituirAoFinal`.

## Evidências

| Prova | Resultado |
|---|---|
| `scripts/tf3004-tdd.py` — CP-F3-006/007 + CA-3-009/011, três papéis | **32/32 PASS** |
| Regressão da task anterior (`tf3003-tdd.py`) | **56/56 PASS** |
| QA Skip (setup · static · build · integrations) | **OK** na v0.0.188 e na v0.0.189 |
| Proposta 2026-0017 (do champion) | **intacta** — rascunho, v1, sem `substituido_por` |

Pedido de prova próprio: **`PROVA-TF3004-0001`** (nunca um pedido real de cliente).

## Correção do teste humano — 1ª rodada (03/11/2026, v0.0.189)

O champion reportou três pontos. **Os três eram procedentes e os três eram defeito de tela**, não
de lógica:

1. **"Não achei 'Ações da proposta'."** O bloco existia, mas ficava **abaixo** de uma tabela de
   25 linhas e saía da área visível. O champion via o documento e a tabela, não as ações.
2. **"Não achei a tabela 'Histórico desta proposta'."** A tabela existia — mas listava as
   propostas de **todas as linhagens do pedido**, não a linhagem da proposta aberta. No pedido de
   prova isso deu 25 linhas, incluindo linhagens de execuções anteriores do TDD e as marcadas
   `LIMPEZA-PROVA-TF3004` — irrelevantes para quem abre uma proposta.
3. **"Só aparecem 2 versões no seletor: V1 e V3."** O seletor listava só as **vigentes**; a v2
   (substituída) desaparecia, dando a impressão de salto na numeração.

### O que mudou na v0.0.189

- **Histórico por raiz de linhagem** (`proposta_origem`): mostra só as versões do MESMO documento,
  com a versão aberta destacada e marcada "← aberta".
- **Seletor com a linhagem inteira**: `2026-0051 — v3` · `2026-0050 — v2 (substituída)` ·
  `2026-0049 — v1 (substituída)`. Abrir uma substituída entra em **modo consulta**: aviso cinza e
  as ações que escrevem (revisar, condições, amostras) ficam ocultas — nunca oferecer botão que o
  servidor vai negar.
- **Tabela com altura máxima, rolagem própria e cabeçalho fixo**: um histórico comprido não
  empurra mais o resto do painel.

### Prova em tela (03/11)

Verificado no preview, logado como admin, no pedido de prova: histórico com **3 versões**
(v3 rascunho · v2 substituída · v1 substituída), seletor com as **3 versões** e os quatro blocos
(Proposta comercial · Ações da proposta · Versões da proposta · Histórico) alcançáveis na mesma
rolagem. QA Skip OK na **v0.0.189**.

**Lição registrada:** histórico de documento versionado filtra pela **raiz da linhagem** — nunca
"todas as propostas do pedido"; e bloco que cresce vai com altura máxima e rolagem, senão esconde
as ações que estão acima dele.

## 2ª rodada de teste humano — APROVADA (03/11/2026)

Aceite do champion: *"Aparentemente, tudo está da maneira como você descreveu"*, acompanhado de
imagem da tela confirmando os três blocos:

| Bloco na tela | Conteúdo confirmado |
|---|---|
| **AÇÕES DA PROPOSTA 2026-0051** | Imprimir / Salvar PDF · Alterar condições de pagamento · Registrar amostras aprovadas · Marcar como revisada |
| **VERSÕES DA PROPOSTA 2026-0051** | versão 3 · "Restauração da 2026-0049" · Criar nova versão · Voltar para a última versão revisada |
| **HISTÓRICO DESTA PROPOSTA (3 VERSÕES)** | 2026-0051 ← aberta (v3, rascunho) · 2026-0050 (v2, substituída → 2026-0051) · 2026-0049 (v1, substituída → 2026-0050) |

Revalidação de encerramento: **TDD 32/32 PASS** + QA Skip OK na v0.0.189.

**Limpeza da revalidação:** as 6 propostas criadas pelo TDD de encerramento no pedido de prova
(2026-0052 a 2026-0057) foram marcadas como substituídas **por ID** — nunca por critério amplo,
conforme a lição de 01/11. A linhagem 2026-0049 → 2026-0050 → 2026-0051 e a proposta **2026-0017**
do champion ficaram intactas (conferido no banco e na tela).

## Estado

**CONCLUÍDA em 03/11/2026** — aceite do champion após 2 rodadas de teste humano.

| Prova | Resultado |
|---|---|
| TDD `scripts/tf3004-tdd.py` | **32/32 PASS** |
| QA Skip (setup · static · build · integrations) | **OK** na v0.0.189 |
| Proposta 2026-0017 (do champion) | **intacta** |
| STATUS | **4/9 (44%)** |
