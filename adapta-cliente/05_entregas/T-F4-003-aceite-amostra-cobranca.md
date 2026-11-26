# T-F4-003 — Aceite de amostra e cobrança observada

**Task:** T-F4-003 (SPEC-4-002, onda 3) · **Fase:** 4
**Versão:** v0.0.230 → **v0.0.241** · **Data:** 26–27/11/2026
**Champion:** Fábio · **Etapa:** CONCLUÍDA — aceite final "Deu tudo certo" (27/11/2026)

---

## 1. A regra de cobrança, como o champion explicou

Regra de **dinheiro** — registrada palavra por palavra, não reinterpretada.

| Quem é | Visitou a fábrica? | Cobra? | Quem decide diferente |
|---|---|---|---|
| Cliente Florus | — | **Não** | — |
| Não é cliente | **Sim** | **Não** — cortesia | — |
| Não é cliente | Não (telefone/vídeo) | **Sim, valor padrão (R$ 300)** | **Só o gestor comercial** |
| Não é cliente, lead **estratégico** | Não | **Não**, por decisão de estratégia | **Só o gestor comercial** |
| Qualquer caso | — | Valor **diferente** do padrão (mais ou menos) | **Só o gestor comercial** |

> **SEGUNDA AMOSTRA NUNCA É COBRADA.** Recebeu amostra — paga ou cortesia — e não gostou
> (cheiro, textura, desempenho): pede outra e **essa nova amostra não é cobrada, mesmo que seja
> de outra linha**. O ciclo pode repetir sem nova cobrança.

**Nas palavras dele:** *"não cobrar de um cliente que não foi até a Florus é decisão de
estratégia e é do gestor — o vendedor não tem autorização. E se tiver uma alteração de valor,
seja para mais ou para menos, também o gestor deve aprovar."*
Confirmado nesta task: **só o Fábio aprova** cortesia e valor.

## 2. O conflito estrutural e como foi resolvido

A tabela de amostras vive **dentro** do documento da proposta e só é editável em **rascunho**
(provado: `registrar_amostras` em proposta revisada → **400**). Mas o aceite nasce **depois** do
envio, quando o documento já está congelado.

**Solução:** o ciclo de teste é **coleção própria**; o documento fica **intocado**. Para a via
assinada do fechamento existe *Criar nova versão* (T-F3-004), sem regra nova. Reescrever o que o
cliente recebeu continua proibido — foi decisão da Fase 3 e não foi afrouxada.

## 2b. Correção de modelo (champion, 27/11/2026) — dois erros meus

Ele aprovou o bloco ("Deu tudo certo, conforme você explicou") e apontou **dois erros de
concepção** que só quem vende saberia:

1. **"Quando o cliente solicita Amostra o valor de R$ 300,00 inclui vários produtos."**
   Eu cobrava **por produto**: na Maria, 3 produtos viravam **R$ 900**. A amostra é **um kit**:
   R$ 300, uma vez, com quantos produtos ela tiver. Corrigido — a cobrança passou a ser única por
   **amostra (proposta + rodada)**, e aprovar o 2º e o 3º produto da mesma amostra não cobra de
   novo.
2. **"a decisão de não cobrar pela 'Amostra' não depende da aprovação dos produtos enviados."**
   Eu tratava a aprovação como gatilho de cobrança. Não é: o cliente pode aprovar 1, 2 ou nenhum
   produto, e a isenção continua sendo só cliente, visita à fábrica ou decisão estratégica do
   gestor.

E duas regras que ele acrescentou:

- **Frete internacional é à parte.** R$ 300 vale para destinatário **no Brasil**. Fora do Brasil,
  o frete é calculado pelo **endereço** e pela **empresa de remessa** — o sistema **registra, não
  calcula**.
- **Aprovou os produtos → os R$ 300 voltam como desconto no valor do projeto**, na produção.
  E, na decisão seguinte: **a recusa não perde o reembolso** — se recusou a 1ª (cobrada), pediu
  outra (não cobrada) e aprovou na 2ª, **os R$ 300 da primeira também são reembolsados**. No fim
  o cliente paga uma amostra só, e ela volta como abatimento.

**Implementação da correção:** migration **0050** (cobrança por amostra/rodada, frete e reembolso)
e **0051** (o país de destino no aceite — o campo não existia e **toda amostra era tratada como
Brasil**, inclusive a do cliente na Argentina). As linhas antigas do ensaio, cobradas por produto
pelo modelo errado, foram **marcadas como pendentes com o motivo escrito — nada foi apagado**.

**Prova da correção: 18/18 PASS** (`scripts/tf4003b-tdd.py`), em proposta de ensaio própria e
limpa, e a bateria original revalidada em **31/31**. Já não é possível cobrar duas vezes pela
mesma amostra: 3 produtos → **uma** cobrança de R$ 300.

## 3. O que foi construído

**Migration 0048** — `amostras_aceite` (o teste como fato: rodada, amostra anterior, estado,
quem aprovou, motivo da recusa, cobrada/causa), `cobrancas` (valor, valor padrão, causa de
isenção, estado do dinheiro, autorizador), `config_cobranca` (valor e **lista de aprovadores**
configurados, semeados com R$ 300 e `fabio@florus.com.br`); os três fatos no pedido
(`eh_cliente`, `visitou_fabrica`, `visita_em`, `lead_estrategico`); 4 alçadas novas.

**Migration 0049** — correção de raiz: as coleções novas nasceram **sem `created`/`updated`**, e
o sort por `-created` lançava erro que o `catch` engolia. Ver §5.

**Hooks:** `amostra_aceite.js` (aceite + decisão da cobrança), `amostra_cortesia.js` (os três
fatos, com de→para na Trilha), `amostra_aprovacoes.js` (rota de leitura + as duas ações do Fábio).

**Tela:** bloco "Amostras desta proposta" dentro do cartão da oportunidade, na Fila — os três
fatos com explicação do efeito de cada marcação, uma linha por amostra pré-preenchida do
documento, histórico de rodadas com a causa de cada isenção, e os botões de cortesia/valor
**desabilitados com o motivo** para quem não é o aprovador.

## 4. Prova

**TDD: 31/31 PASS** (`scripts/tf4003-tdd.py`) + **18/18 PASS** na correção de modelo (`scripts/tf4003b-tdd.py`), ambos em cenário de ensaio próprio e limpo.

**Aceite do champion:** 27/11/2026, em duas rodadas — 1ª "Deu tudo certo, conforme você explicou", com as duas correções de modelo pedidas na mesma mensagem; 2ª, depois das correções: **"Deu tudo certo"**.

| Grupo | Provas |
|---|---|
| Ordem do processo | amostra em proposta rascunho negada (400) |
| CP-F4-022 padrão | cobra R$ 300 · fica `observada` · **uma única** cobrança |
| CP-F4-020 cliente | cobrança zerada, causa `cliente` |
| CP-F4-021 visita | cobrança zerada, causa `visita_fabrica` |
| CP-F4-023/025 estratégico | sem autorização **cobra** (fail-closed) · vendedor e gestor comum negados (403) · Fábio autoriza · cobrança zera com causa `estrategia` · **aceite seguinte já sai sem cobrança** · fica na Trilha |
| CP-F4-026/027 valor | vendedor negado (403) · motivo curto negado · Fábio altera para menos · **não reescreve a outra cobrança** |
| CP-F4-028/029/030 recusa | motivo obrigatório · recusa registrada · recusa não gera cobrança · **reposição mesma linha não é cobrada** · aponta para a recusada · **reposição de OUTRA linha também não é cobrada** |
| CP-F4-031/032 | aceite duplicado negado com motivo · vendedor não registra na proposta de outro |
| CP-F4-040 (correção) | **3 produtos do mesmo kit = UMA cobrança de R$ 300** · os três na mesma rodada |
| CP-F4-041 (correção) | aprovar 1 de 3 não muda a cobrança |
| CP-F4-042 (correção) | fora do Brasil marcado como internacional · frete **não calculado** (zero, para o despachante) · valor da amostra separado do frete |
| CP-F4-043 (correção) | reembolso previsto = desconto no projeto |
| CP-F4-044 (correção) | recusa na 1ª + aprovação na 2ª → **os R$ 300 da primeira também ficam reembolsáveis** · só uma amostra é cobrada |

**Migrations 0048, 0049, 0050 e 0051 conferidas como `applied` no schema**, não só o QA dizendo `ok`.
**Prova em tela:** `artifacts/amostras-final.png` — bloco na Fila da Maria com os três fatos, a
explicação do efeito e as três amostras do documento.

## 5. Defeitos encontrados executando (todos meus, todos corrigidos)

> Dois deles só apareceram na **segunda** rodada, quando o champion corrigiu o modelo de cobrança:
> a cobrança por produto (que ele apontou) e o bloqueio da reposição de outra linha (que o TDD
> pegou). Provar de novo depois de corrigir foi o que os trouxe à luz.

1. **Função no topo do arquivo** referenciada dentro do callback → `ReferenceError` no JSVM.
   **O QA novo pegou** (check `integrations`), que antes não existia. Tudo passou a ser inline.
2. **`aceite_id` não era gerado** no hook → `validation_required`. Passou a ser gerado no
   servidor, por contador conferido (nunca pelo navegador — lição da Fase 3).
3. **Contador não avançava**: `findRecordsByFilter` com `~` voltava vazio dentro do hook.
4. **`e.next()` não devolve os campos gravados** → a cobrança era gravada **sem o vínculo** com o
   aceite. O número passou a ser guardado em variável antes do `next()`.
5. **CAUSA RAIZ de 10 falhas:** as coleções novas foram criadas **sem `created`/`updated`**; o
   sort `-created` lançava `invalid sort field "created"` e o `catch` vazio devolvia lista vazia —
   então a rodada era sempre 1 e a reposição batia no índice único. **Erro de leitura escondido
   parecia ausência de dado.** Migration 0049 cria os carimbos e o `catch` passou a **dizer** a
   falha em vez de engoli-la.
6. **Defeito de regra:** eu bloqueava a reposição da mesma linha depois de recusa — o caso que o
   champion descreveu. Corrigido.
7. **Defeito de regra (o TDD pegou):** eu só olhava a recusa da **mesma linha**, e cobrava a
   reposição de **outra linha**. A regra dele é explícita: não cobra, mesmo de outra linha.
   Corrigido: a referência é a última amostra registrada **na proposta**.

## 6. Resíduos declarados

- A coleção tem **retenção** (`deleteRule: null`, decisão da Fase 3 — trilha de dinheiro não se
  apaga), então os aceites e cobranças do ensaio **ficaram no banco**. Todos com identificador de
  ensaio (`PROVA-*`) e em propostas de ensaio (`2026-0079` e as `PROVA-T3-*`/`PROVA-F4E-*`), que
  **não aparecem na fila do champion**.
- **9 cobranças excedentes** do modelo antigo (uma por produto) ficaram com estado `pendente` e o
  motivo escrito, para conferência — foi o que permitiu criar o índice único por amostra sem
  apagar dinheiro.
- **A proposta 2026-0077 (Maria) não foi alterada**: 3 amostras, 0 aprovadas, documento intacto.
- **O pedido real 20260921-0005 não foi alterado.**
- Resíduo a limpar quando o champion autorizar: aceites/cobranças de ensaio na proposta 2026-0079.
  Retenção impede apagar pela API — a limpeza é por migration, se ele quiser.

## 7. Pendências desta task

- ~~P-F4-003-a: teste humano~~ — **aprovado em 27/11/2026**, em duas rodadas ("Deu tudo certo").
- **P-F4-003-b:** o valor muda por quantidade — ficou decidido **sem tabela de faixas**: R$ 300 é
  o padrão e cada exceção passa pelo Fábio com motivo. Se um dia houver tabela por faixa, é
  configuração nova, não reescrita.
- **P-F4-003-c:** pagamento confirmado, pendência de divergência, crédito e reembolso são a
  **T-F4-004** — aqui o dinheiro fica em `observada`, que é o estado correto desta task.
