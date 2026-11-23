# T-F3-006 — Envio registrado e confirmação de recebimento

**Versão:** v0.0.204 · **Data:** 23/11/2026 · **Etapa:** aguardando teste humano

## Decisões do champion que definiram esta entrega

1. **Entrega manual registrada (opção A).** O vendedor gera o PDF pelo sistema, baixa e envia
   pelo WhatsApp dele. O sistema **não envia nada** — registra o ato. Sem custo, sem credencial,
   sem dependência externa.
2. **O sistema é o dono do controle.** O DataCrazy será desativado quando o Skip estiver pronto;
   nada é integrado a ele.
3. **Dois atos, não três** — o champion recusou meu desenho de dois marcos separados
   (recebimento + entendimento) com um argumento correto: acontecem na mesma ligação.
   - **ENVIO:** data/hora + canal (WhatsApp ou e-mail) + destinatário.
   - **CONFIRMAÇÃO DE RECEBIMENTO:** um ato único que significa *"o cliente recebeu e as dúvidas
     foram esclarecidas"*. Exige dizer com quem do lado do cliente se falou.
   - Motivo declarado por ele: o vendedor envia e encerra o processo; o objetivo é fazê-lo
     **falar com o cliente**. Um campo por ato real — não processo enfeitando o sistema.

## O que foi construído

- **Migration 0041** — coleção `envios`: `envio_id` (ENV-ANO-####), tipo e id do documento,
  versão, pedido, cliente, `enviado_em`, `canal`, `destinatario`, quem registrou, `confirmado_em`,
  `confirmado_por`, `contato_confirmacao`, `meio_confirmacao`, observações, `estado`
  (`enviado`/`confirmado`/`falhou`) e `pendencia_reconciliacao`. RLS: vendedor vê os próprios,
  gestor/admin veem tudo. `deleteRule: null` = retenção. Duas alçadas novas na matriz
  (`registrar_envio`, `confirmar_recebimento`, mínimo vendedor).
- **Hook `registro_envios.js`** — servidor decide, a tela não: rascunho é negado com motivo
  auditado; fail-closed sem alçada; idempotência por documento+versão+destinatário; confirmação
  exige contato e não se repete; o fato do envio (data, canal, destinatário) não é reescrito;
  `DELETE` negado.
- **Tela `EnvioPainel`** + botão **"Envios"** no menu: registrar envio (só lista proposta
  revisada), confirmar recebimento na própria linha do envio, painel do gestor com as enviadas
  **sem confirmação** e há quanto tempo esperam.

## Prova

`TDD 38/38 PASS` (`scripts/tf3006-tdd.py`) — CA-3-017…022 + alçadas + retenção + ordem.
QA Skip OK na v0.0.204. Tela verificada no preview logado como admin.

**O cenário do caminho positivo foi produzido pelo caminho REAL**, não injetado: pedido com
produto do catálogo → montagem da proposta → nova versão com amostras → revisão. Se o motor
real não produzisse uma proposta revisável, o teste falharia em vez de forjar o estado.

## Defeito real encontrado pelo TDD

`documento_versao` gravava **0**: eu usava `getString` num campo `number`, que devolve vazio, e o
fallback preenchia zero. Corrigido para `getInt` direto do documento na v0.0.203.

## O que esta entrega NÃO faz, dito com clareza

- **Não envia mensagem.** O envio é do vendedor, no WhatsApp dele. O sistema registra.
- **Não prova entrega nem entendimento.** Registra a declaração do vendedor, com autor, data e
  versão. É rastreabilidade, não certificação.
- **Não cobra prazo.** O painel mostra há quanto tempo espera, sem alerta automático — o champion
  pediu para não encher o sistema de processos.

## Cenário deixado para o teste humano

Proposta **2026-0075** em estado `revisada`, do pedido `DEMO-ENVIO` (cliente fictício
"Cliente Demonstração"), criada pelo caminho real do sistema. É ela que aparece no seletor da
tela **Envios** para o champion testar o registro.

## Higiene

45 propostas de prova do TDD marcadas como substituídas **por ID**; 6 pedidos de prova arquivados;
8 envios mantidos (retenção impede apagar) e marcados como registro de teste na observação.
Nenhuma proposta real foi tocada.
