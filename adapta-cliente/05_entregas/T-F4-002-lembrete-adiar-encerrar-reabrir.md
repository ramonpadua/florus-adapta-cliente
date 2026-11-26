# T-F4-002 — Lembrete, adiamento, encerramento e reabertura da oportunidade

**Task:** T-F4-002 (SPEC-4-001, onda 2) · **Fase:** 4 · **Versão:** v0.0.228 → **v0.0.229** (correção)
**Champion:** Fábio · **Data:** 26/11/2026 · **Etapa:** CONCLUÍDA — aceite "Deu tudo certo"

---

## 1. O que passou a existir

A fila de oportunidades deixou de ser só uma lista para ler: cada oportunidade agora tem
**Concluir**, **Adiar**, **Encerrar** e **Histórico**, e o menu mostra quantas passaram do prazo.

| Ato | O que faz | Exige | Quem pode |
|---|---|---|---|
| **Concluir** | registra que a conversa com o cliente aconteceu; tira da fila | — (motivo opcional) | vendedor dono, gestor, admin |
| **Adiar** | muda a data combinada e conta a vez | motivo ≥ 10 + nova data posterior | vendedor dono, gestor, admin |
| **Encerrar** | tira da fila **sem apagar** | motivo ≥ 10 | vendedor dono, gestor, admin |
| **Reabrir** | devolve ao trabalho preservando o histórico | motivo ≥ 10 | vendedor dono, gestor, admin |
| **Histórico** | mostra tudo o que aconteceu, com quem, quando e por quê | — | vendedor dono, gestor, admin |

**Lembrete: só na tela.** O contador âmbar no menu *Fila* e o destaque do cartão são o lembrete.
Nada é enviado por e-mail nem por WhatsApp — decisão do champion em 26/11, tomada depois de saber
que o ambiente de teste não envia e-mail e que o relay tem limite de 10 destinatários por hora.

## 2. Decisões do champion que estão no código

- **SLA padrão 3 dias**, contado do envio, configurável por gestor/admin com motivo na Trilha.
  Adiar muda a data combinada mas **não reescreve o SLA**: o que mudou foi o combinado, com motivo.
- **Adiar, encerrar e reabrir: vendedor dono + gestor/admin.** Cada transição tem sua linha na
  matriz de alçadas e é **negada se a linha não existir** (fail-closed).
- **Regra da fase:** o follow-up **não envia mensagem, não negocia, não desqualifica e não fecha**.
  As ações `enviar_mensagem_ao_cliente` e `fechar_oportunidade` são **negadas com 400** — a lista
  de ações válidas é fechada (`adiar`, `concluir`, `encerrar`, `reabrir`).
- **Encerrar não apaga.** O registro continua no banco com motivo, autor e data; o histórico
  anterior é preservado inteiro, e reabrir não apaga o carimbo de conclusão.

## 3. O que foi construído

- **Migration 0046:** campos `motivo_ultima_transicao`, `ultima_transicao_em`,
  `ultima_transicao_por`, `adiamentos`, `encerrado_em`, `concluido_em` em `followups`; abre a
  edição da coleção (a regra de quem pode o quê fica no hook, não na RLS, porque a RLS nega antes
  do hook e devolveria 404 sem motivo); quatro alçadas novas na matriz.
- **Hook `followup_transicoes.js`:** as quatro transições, alçada fail-closed, posse, motivo,
  transições válidas e evento na Trilha com de→para. Um hook por arquivo.
- **Hook `followup_historico.js` (v0.0.229):** rota `GET /followup-historico?followup=<id>`, que
  confere a posse e devolve o histórico **daquela oportunidade**, separando o que aconteceu do
  que foi recusado pelo sistema.
- **Rota da fila:** passou a devolver `adiamentos`, `ultimo_motivo`, `vence_hoje` e um modo
  `?resumo=1` com **apenas contadores** para o aviso do menu (sem dado de cliente).
- **Tela `FilaOportunidades`:** aviso de lembrete, bloco "Ações da fila" com legenda de uma linha
  por botão, os quatro botões dentro do cartão da oportunidade, formulário que abre **no próprio
  cartão** e histórico com rolagem própria.
- **Menu:** contador âmbar de vencidas, atualizado a cada 2 minutos.

## 4. O defeito que o champion encontrou (e a correção)

O teste humano aprovou 4 dos 5 passos. **O Histórico falhou:** ele adiou duas vezes e a tela
disse "Nenhum evento registrado ainda".

**Causa — três erros meus, em cadeia:**

1. **Chave errada.** A tela filtrava a Trilha pelo **pedido**, não pela **oportunidade**. Um pedido
   pode ter mais de uma oportunidade; filtrar pelo pedido misturaria históricos.
2. **Codificação dupla.** O filtro era montado já codificado (`encodeURIComponent`) e o SDK do
   PocketBase codificava de novo → a API respondia **400**.
3. **Erro escondido.** O `catch` estava vazio (`catch { setHistorico([]) }`), então o 400 virava
   **"nenhum evento"**. O usuário via ausência de dado onde havia falha de leitura.

**Correção (v0.0.229):**

- **Migration 0047:** campo `followup_id_ref` na Trilha; o hook passou a gravá-lo e o passado foi
  preenchido **com critério** — só onde o pedido tem **exatamente uma** oportunidade. Onde há mais
  de uma, a referência ficou em branco de propósito: palpite em trilha é pior que lacuna visível.
- **Rota própria de histórico** com conferência de posse: a Trilha continua legível só por
  gestor/admin (é o que a mantém íntegra), mas o **vendedor dono** agora vê o histórico do próprio
  trabalho — antes nem isso era possível.
- **A falha aparece na tela:** se a leitura falhar, o cartão mostra "Não consegui ler o histórico"
  com o motivo e um botão *Tentar de novo*. Lista vazia passou a significar só lista vazia.

## 5. Prova

**TDD: 31/31 PASS** (`scripts/tf4002-tdd.py`) contra o backend real:

| Grupo | Provas | Resultado |
|---|---|---|
| CP-F4-010 adiar | motivo inválido negado e prazo intacto · sem data negado · com motivo/data muda o prazo · incrementa contador e mantém SLA · não sai da fila · de→para na Trilha · data anterior negada | 7/7 |
| CP-F4-011 concluir | estado e carimbo · sai da fila | 2/2 |
| CP-F4-012 encerrar | sem motivo negado · estado e carimbo · **não apaga** · sai da fila · encerrar de novo negado | 5/5 |
| CP-F4-013 reabrir | sem motivo negado · volta a aberta **preservando o carimbo de conclusão** · histórico inteiro | 3/3 |
| CP-F4-014/015 | vendedor não mexe na de outro (404) · "enviar mensagem" negada · "fechar oportunidade" negada | 3/3 |
| CP-F4-016 lembrete | contadores no resumo · não vaza cliente · concorda com a lista | 3/3 |
| CP-F4-017 histórico | eventos da oportunidade · negados separados · autor/data/motivo/de→para · independente por oportunidade · sem id negado | 5/5 |
| CP-F4-018 posse | vendedor não lê o de outro (403) · sem login (401) | 2/2 |
| Restauração | oportunidade de ensaio devolvida para `aberta` | 1/1 |

**Migrations 0046 e 0047 conferidas como `applied` no schema** — não só o QA dizendo `ok`.

**Prova em tela** (`artifacts/fila-historico-corrigido.png`): o Histórico da Maria Erotides SA
mostra os **2 registros** criados pelo champion no teste — 26/11 07:23 (prazo 28/11 → 29/11,
"O cliente pediu para aguardar") e 26/11 07:24 (29/11 → 30/11, "Nova explicação...") — com autor
e papel. O cartão continua com a ação principal visível acima da lista.

**Aceite do champion:** 26/11/2026, re-teste do passo corrigido — **"Deu tudo certo"**.

## 6. Limpeza e resíduos declarados

- O ensaio usou **FU-2026-0002** (oportunidade de prova da T-F3-008, cliente "PROVA T-F3-008
  (ensaio)"), que **não aparece na fila do champion** — o servidor filtra ensaio pelo nome do
  cliente. Ela foi devolvida ao estado `aberta` ao final, com o contador de adiamentos que os
  testes acumularam e o histórico completo na Trilha.
- Nada foi apagado: `envios` e `followups` têm retenção. Os eventos de teste estão na Trilha com
  o motivo escrito, identificáveis pelo texto.
- **O pedido real 20260921-0005 não foi alterado.**

## 7. Pendências desta task

- **P-F4-002-b:** 14 eventos antigos de follow-up ficaram sem `followup_id_ref` de propósito — são
  de pedidos com mais de uma oportunidade (o ensaio da Fase 3 tem duas). Eles não aparecem no
  histórico de nenhuma das duas; é lacuna visível, não palpite.
