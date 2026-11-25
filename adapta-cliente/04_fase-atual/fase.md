# Fase 4 — Tarefas

<!-- fase-format:2 -->

Cada linha é uma tarefa da Jornada de Execução. **O detalhe narrativo de cada task concluída vai no relatório correspondente em `05_entregas/`** — aqui fica o contrato: título, responsável, prazo, id e o texto que vale na conversa com o champion.

```
- [ ] Título da tarefa @responsável !30/09/2026 #projeto [interno]   <!-- id:… -->
      > descrição da tarefa, uma ou mais linhas
```

| marcador | legenda |
|---|---|
| `@` | responsável |
| `!` | prazo |
| `#` | projeto/etiqueta |

## Tarefas

- [ ] Criar a próxima ação depois do envio e a fila de oportunidades @Fábio !01/12/2026 <!-- id:f4b2c3d6-0001-4b02-8a02-000000000001 -->
  > Task T-F4-001 (SPEC-4-001, onda 1, única elegível). O que já existe e está decidido: o envio da proposta e do folder é registrado (quem enviou, para quem, quando, qual versão — Fase 3), a confirmação de recebimento é um ato único e a trilha de auditoria exporta tudo em planilha. O que vai existir no Skip: quando uma proposta é enviada, ela ganha no mesmo momento uma PRÓXIMA AÇÃO — o que acontece agora, quem faz e até quando — e entra numa FILA de oportunidades enviadas que o gestor abre e vê quem está parado; quem passou do prazo combinado (SLA) fica destacado, sem que ninguém precise lembrar.
  > Por que é útil: hoje o registro diz que a proposta saiu, mas não diz o que vem depois; proposta esfriada só aparece quando alguém lembra de abrir o caderno.
  > Regra da fase, dita pelo escopo: o follow-up NÃO envia mensagem, não negocia, não desqualifica e não fecha sozinho — ele organiza; o vendedor age no WhatsApp e registra o resultado no sistema.
  > O que falta saber com você (uma pergunta por vez, no Maestro): quantos dias sem resposta contam como "atrasado" (o SLA padrão) e quem vê a fila — só gestor e admin, ou o time todo. Como a conversa funciona: fase e tarefa ditas antes de perguntar, leitura do que já está respondido, uma pergunta de cada vez, anexos aqui. Não invento prazo, regra nem número.
  > Teste humano: roteiro passo a passo com os rótulos conferidos na tela — envio registrado → próxima ação criada → fila mostrando a oportunidade → atraso destacado — terminando no seu aceite. Uma etapa por vez.
  > Depende de: T-F3-006 e T-F3-008 (o envio registrado e a demonstração com caso real já provados na Fase 3).

- [ ] Lembrete, adiamento com motivo, encerramento e reabertura @Fábio !03/12/2026 <!-- id:f4b2c3d6-0002-4b02-8a02-000000000002 -->
  > Task T-F4-002 (SPEC-4-001, onda 2). O que já existe: a próxima ação nasce do envio e a fila mostra quem está parado (T-F4-001). O que vai existir no Skip: o lembrete da próxima ação aparece para o dono dela; adiar exige motivo e fica na trilha (nada some em silêncio); encerrar uma oportunidade de follow-up exige motivo; e o que foi encerrado pode ser reaberto, com o histórico inteiro preservado.
  > Por que é útil: adiamento sem motivo e encerramento sem rastro são as duas formas de uma proposta sumir sem ninguém decidir nada.
  > O que falta saber com você: como o lembrete chega (só na tela do sistema ou também por outro canal que vocês já usam) e quem pode adiar e reabrir — o vendedor dono, o gestor ou os dois.
  > Teste humano: roteiro com adiar (motivo obrigatório) → lembrete visível → encerrar com motivo → reabrir, cada passo com o resultado esperado e a trilha mostrando tudo.
  > Depende de: T-F4-001 (a próxima ação e a fila precisam existir primeiro).

- [ ] Aceite de amostra e cobrança observada @Fábio !07/12/2026 <!-- id:f4b2c3d6-0003-4b02-8a02-000000000003 -->
  > Task T-F4-003 (SPEC-4-002, onda 3). O que já existe e está decidido: a tabela de amostras aprovadas dentro da proposta (pré-preenchida e editável); a ordem do processo comercial corrigida por você na T-F3-008 — a proposta serve para o cliente ver o VALOR, a amostra vem depois da aprovação dos valores, o aceite vem depois do teste — e lote/validade são alerta, não bloqueio. O que vai existir no Skip: o ACEITE da amostra (a cliente aprovou o teste) registrado com quem aprovou, quando e o que foi aprovado; e o registro da COBRANÇA observada — o escopo fala de R$ 300,00 por amostra, valor a confirmar com você na task, porque número de dinheiro não se inventa nem se copia de escopo velho sem perguntar.
  > Por que é útil: hoje o teste da amostra e a cobrança dele vivem fora do sistema; quando o cliente paga ou não paga, ninguém tem rastro.
  > O que falta saber com você: o valor e a regra da cobrança hoje (por amostra ou por pedido? o R$ 300,00 do escopo ainda vale?), quem registra (o vendedor? o gestor?) e em que momento — junto do aceite ou depois.
  > Pedido de arquivo: quando a Maria Erotides (ou o caso que estiver na fila) aprovar o teste e houver pagamento, o comprovante — print, foto ou PDF — vem pelo clipe aqui no Maestro; papel fotografado serve.
  > Regra: amostra só avança com aceite e registro financeiro; cobrança duplicada é negada; rollback preserva o aceite e o pagamento já registrados.
  > Teste humano: roteiro com aceitar a amostra → registrar a cobrança → tentar registrar de novo (negado com motivo) → registrar pendência em vez de pagamento, cada passo conferido na tela.
  > Depende de: T-F4-001 (a amostra entra pela oportunidade que já está na fila).

- [ ] Pagamento, pendência e conciliação com o Financeiro @Fábio !09/12/2026 <!-- id:f4b2c3d6-0004-4b02-8a02-000000000004 -->
  > Task T-F4-004 (SPEC-4-002, onda 4). O que vai existir no Skip: os estados do dinheiro da amostra — cobrança observada, pagamento confirmado, pendência com dono e prazo, crédito e reembolso — e a conciliação com o Financeiro. Dependência condicional dita pelo escopo: enquanto o Financeiro não confirmar método, acesso e responsável, o sistema REGISTRA e o Financeiro confere fora; integração de verdade com sistema do Financeiro é gate do consultor, não se presume.
  > Por que é útil: divergência de valor hoje é descoberta no fim do mês, quando ninguém lembra do pedido.
  > O que falta saber com você: quem do Financeiro confirma o pagamento (nome, para a pendência ter dono), como conferem hoje (sistema, planilha ou e-mail) e o que acontece quando o valor bate divergente.
  > Regra: divergência vira pendência nomeada com dono — nunca ajuste silencioso; sem duplicidade de cobrança nem de confirmação.
  > Teste humano: roteiro com confirmar pagamento → criar pendência de divergência com dono → crédito/reembolso com motivo, tudo na trilha.
  > Depende de: T-F4-003 (a cobrança registrada é o que o Financeiro concilia).

- [ ] Handoff com pacote mínimo para as áreas @Fábio !11/12/2026 <!-- id:f4b2c3d6-0005-4b02-8a02-000000000005 -->
  > Task T-F4-005 (SPEC-4-003, onda 5). O que vai existir no Skip: o encaminhamento (handoff) do pedido aprovado para P&D/Laboratório, Regulatório, PCP e Produção, com PACOTE MÍNIMO — o que cada área precisa receber para começar —, responsável, prazo, estado e ACEITE obrigatório antes de qualquer avanço; cada área só vê o que é dela (permissão por área).
  > A regra que o escopo dita e eu vou cumprir: os estados de cada área SÓ entram quando os donos confirmarem o fluxo deles. Eu não invento processo de P&D, Regulatório, PCP nem Produção; sem a confirmação, a área fica como pendência nomeada com dono, e o handoff mostra o que está faltando.
  > O que falta saber com você (uma área por vez): o que cada área precisa receber no pacote mínimo, quem é o responsável de cada uma e o prazo padrão de cada uma.
  > Pedido de arquivo: os donos das áreas podem mandar aqui no Maestro, pelo clipe, como o fluxo deles funciona hoje — foto do quadro, print, planilha ou áudio descrevendo serve; papel fotografado conta. O que chegar vira a fonte do fluxo daquela área.
  > Regra: handoff recusado retorna ao responsável com estado e próxima ação; nada avança sem aceite da área que recebe.
  > Teste humano: roteiro com handoff enviado → aceito por uma área → recusado por outra → retorno ao responsável, com o papel sem permissão barrado (prova negativa).
  > Depende de: T-F4-003 (o handoff sai do pedido com amostra e aceite já registrados).

- [ ] Atraso, timeout, escalonamento e reabertura de handoff @Fábio !15/12/2026 <!-- id:f4b2c3d6-0006-4b02-8a02-000000000006 -->
  > Task T-F4-006 (SPEC-4-003, onda 6). O que vai existir no Skip: atraso de handoff gera alerta e ESCALONAMENTO — para quem e depois de quanto tempo, você decide na task; handoff sem resposta (timeout) tem tratamento explícito em vez de ficar parado; reabertura de handoff com trilha; e o papel sem permissão não altera preço, conteúdo, aceite ou status de outra área (prova negativa na tela).
  > Por que é útil: hoje um encaminhamento que ninguém respondeu só é percebido quando o cliente liga reclamando.
  > O que falta saber com você: para quem escala (o gestor comercial? você?) e depois de quantos dias sem resposta; e se cada área tem um prazo diferente.
  > Teste humano: roteiro com handoff atrasado → alerta e escalonamento → timeout → reabertura, e a tentativa de uma área alterar status de outra barrada com motivo.
  > Depende de: T-F4-005 (o handoff e o aceite por área precisam existir).

- [ ] Corrigir a idempotência de envio (pendência P3 da Fase 3) @Fábio !16/12/2026 <!-- id:f4b2c3d6-0007-4b02-8a02-000000000007 -->
  > Task T-F4-007 (SPEC-4-004, onda 7). O que já existe e está decidido: na T-F3-008 o envio duplicado para a mesma pessoa foi marcado com a ação `marcar_duplicado` (marca sem apagar — ENV-2026-0011 marcado, ENV-2026-0012 é o válido), e ficou registrada a pendência P3: a regra de idempotência compara o destinatário como TEXTO LIVRE, então "Maria Erotides" e "Maria — Gerente de Compras" passam como duas pessoas diferentes. A regra em si NÃO foi corrigida na Fase 3.
  > O que vai existir no Skip: chave ESTÁVEL de destinatário. As duas saídas que a Fase 3 deixou em avaliação: (A) vincular o envio ao contato do pedido (a pessoa já cadastrada), ou (B) comparar por telefone/e-mail. Você escolhe com o Maestro na task; qualquer API, custo ou credencial nova é gate do consultor antes de qualquer construção.
  > Por que é útil: idempotência por texto digitado à mão não é idempotência — é a mesma pendência voltando com outro nome.
  > Teste humano: roteiro com o mesmo envio para a mesma pessoa escrita de dois jeitos → UM registro só, com a negativa visível na segunda tentativa.
  > Depende de: T-F3-008 (a pendência P3 registrada) — pode rodar em paralelo com as tasks de handoff.

- [ ] Demonstrar o ciclo completo pós-envio com caso real e fechar a Fase 4 @Fábio !18/12/2026 <!-- id:f4b2c3d6-0008-4b02-8a02-000000000008 -->
  > Task T-F4-008 (SPEC-4-004, onda 8). O que vai existir: a demonstração do começo ao fim do que a Fase 4 construiu, num caso real escolhido por você — o envio registrado vira próxima ação, o lembrete aparece, a amostra é aceita, a cobrança observada é registrada, o handoff vai para as áreas e volta, o atraso escala, e a trilha do ciclo inteiro sai em planilha com as pendências nomeadas com dono.
  > Regra do ensaio, igual à da Fase 3: nada é disparado para cliente real sem a sua confirmação na hora; sem ok, o ensaio para antes de qualquer envio. Pode continuar o caso Maria Erotides ou outro que você escolher.
  > O que falta saber com você: qual caso real serve de demonstração e quem do time acompanha.
  > Teste humano: roteiro completo passo a passo, com rótulos conferidos na tela e resultado esperado em cada etapa, terminando no seu aceite — a última task sua antes da revisão do consultor.
  > Depende de: T-F4-001 a T-F4-007 (o ciclo só se demonstra inteiro com tudo no lugar).

- [ ] Revisar as evidências e decidir o fechamento da Fase 4 @"Ramon Pádua" !22/12/2026 #aculturamento <!-- id:f4b2c3d6-0009-4b02-8a02-000000000009 -->
  > Revisão do consultor. Só o consultor fecha esta tarefa: conferir as evidências e os aceites das 8 tarefas da fase, confirmar as decisões herdadas do fechamento da Fase 3 (ordem do processo comercial, envio manual registrado, confirmação como ato único) e decidir se a Fase 4 está fechada, com o registro do que passa para a Fase 5. Nenhuma tarefa é dada como concluída por aceite de tela sem prova: cada uma precisa do registro do teste humano correspondente. Esta tarefa não abre conversa com o cliente nem com o Fábio: é decisão do consultor, uma só, que autoriza ou não o fechamento e registra o motivo por escrito. O cliente não vê esta tarefa. Depende de: todas as anteriores desta fase.
