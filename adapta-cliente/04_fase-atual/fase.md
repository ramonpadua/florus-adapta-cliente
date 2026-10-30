# Fase 3 — Tarefas

<!-- fase-format:2 -->

Cada linha é uma tarefa da Jornada de Execução. **Tudo que cabe num card cabe nesta linha** — se um
campo não estiver aqui, ele não tem como ser preenchido, porque é este arquivo que cria a tarefa.

```
- [ ] Título da tarefa @responsável !30/09/2026 #projeto [interno]   <!-- id:… -->
      > descrição da tarefa, uma ou mais linhas
  - [ ] subtarefa (basta indentar 2 espaços)                         <!-- id:… -->
    - [ ] sub-subtarefa (indente mais 2)                             <!-- id:… -->
```

| marcador | legenda | o que define | se você não escrever |
|---|---|---|---|
| `- [ ]` / `- [/]` / `- [x]` | | a fazer / em andamento / concluída | a fazer |
| `@nome` | | responsável (`@"Nome Composto"` com aspas) | fica **sem responsável** |
| `!dd/mm/aaaa` | | prazo | fica **sem prazo** |
| `#projeto` / `#aculturamento` | | tipo | Projeto de IA |
| `[interno]` | | o cliente **não** vê esta tarefa | o cliente vê |
| `> texto` na linha de baixo | | descrição (aparece ao abrir o card) | sem descrição |
| indentar 2 espaços | | vira subtarefa da tarefa acima (vale em qualquer profundidade) | tarefa de topo |

Os marcadores só valem **no fim da linha** — `Revisar #3 do contrato` continua sendo um título.
Um título que TERMINA na forma de um marcador sai escapado com `\\` (`Ligar para \\@joao`); a barra é
só para o parser e nunca aparece no card. Você não precisa escrever isso à mão.
Marque `[x]` para concluir e adicione linhas novas à vontade: elas entram no quadro na próxima
sincronização e voltam aqui com o `<!-- id:… -->` preenchido. **Não apague o marcador de id** das
tarefas que já têm um.

- [x] Definir conteúdo aprovado: estrutura da proposta e do folder, fontes e donos @Fábio !03/11/2026  <!-- id:f3a1c2d4-0001-4a01-9a01-000000000001 -->
  > Task T-F3-001 (SPEC-3-001, onda 1, única elegível). Modelar a coleção `conteudos` com RLS, trilha de versão e exclusão bloqueada (padrão Fase 2): proposta e folder como documentos versionados, cada bloco com fonte, vigência e responsável. Insumo embutido (responder dentro da task): quais blocos compõem a proposta (capa, entendimento, escopo, investimento, condições) e do folder (linhas, produtos, alegações), quem aprova cada bloco e qual fonte oficial alimenta cada um. Conteúdo sem fonte/vigência/aprovação nunca visível ao vendedor (padrão RN-2.002). Prova: TDD GREEN com CP-F3-001. Evidência: exportação da coleção + capturas + decisão registrada. **Concluída em 2026-10-28:** champion "Concluí o teste e tudo deu certo" + 2 considerações atendidas (selo com cor semântica e confirmação na própria linha do bloco, v0.0.170–171); TDD CP-F3-001/002/003 32/32 PASS; estrutura aprovada = 6 blocos de proposta + 5 de folder, gestor/admin aprova todos, três fontes por bloco, vigência 31/12/2026.
- [x] Migrar e aprovar o conteúdo piloto, com pendências nomeadas @Fábio !05/11/2026  <!-- id:f3a1c2d4-0002-4a01-9a01-000000000002 -->
  > Task T-F3-002 (SPEC-3-001, onda 2, após T-F3-001). Migrar o conteúdo real (texto da proposta e do folder) com inventário e reconciliação item a item contra as fontes declaradas; aprovar bloco a bloco com ator, data, versão e motivo; pendências nomeadas por bloco incompleto — nada usado em silêncio. Prova: TDD REGRESSÃO com CP-F3-002/003. Evidência: relatório de reconciliação + aceite do champion. **Implementada em 2026-10-29 (v0.0.172–175):** 8 blocos de proposta (2 novos: matérias-primas e procedimentos) + campos de conferência (CA-3-004/006); 5 blocos migrados do PDF Proposta 2026-0212 com conferência item a item; alertas verdes e as 20 etapas preservados; assinatura e destaque das amostras no bloco de próximos passos; TDD CP-F3-002/003 22/22 PASS. **Concluída em 2026-10-29:** aceite do champion ("Sim, pode concluir a T-F3-002") após teste nos dois papéis (admin 5 aprovado · 2 rascunho · 1 pendente; vendedor vê os 5 aprovados e os pendentes "sem versão", sem texto); revalidação de encerramento 22/22 + 11/11 PASS; emenda de usabilidade v0.0.176 (Limpar seleção no campo Fonte e rótulo do Motivo sem ambiguidade).
- [ ] Montar a proposta do cliente com o catálogo que já existe @Fábio !10/11/2026  <!-- id:f3a1c2d4-0003-4a01-9a01-000000000003 -->
  > Task T-F3-003. O que já existe: o pedido que o vendedor registra e o catálogo com preços aprovados, com o motor de investimento já montado. Não peça de novo o que já foi feito: os alertas de conteúdo e as etapas do PDF da proposta migrada continuam valendo e não devem ser recriados.
  > O que vai existir no Skip: um botão que monta a proposta a partir daquele pedido, já com os blocos de texto aprovados, a tabela de produtos com preço vigente, o investimento separado por item e as condições e a validade. A proposta nasce como rascunho e não pode ser enviada antes de o vendedor revisar.
  > Por que é útil: hoje a proposta é montada à mão, item por item; com isso o vendedor parte de um documento já preenchido e só confere o que é do cliente.
  > O que falta saber com você: qual é a linha de atuação ou o tipo de cliente desta proposta piloto, para o texto sair personalizado em vez de genérico; o que a empresa entende do problema do cliente; quais ativos e materiais podem ser citados (obras, laudos, cases, fotos, certificações); qual é a função do contato que recebe a proposta e para que ele vai usar o documento; e, só se ainda não existir material aprovado, o layout e as referências que você quer ver no resultado. Se a fonte de algum bloco não estiver aprovada, ela vira pendência com nome e dono, nunca é preenchida no silêncio.
  > Como funciona a conversa: eu digo a fase e a tarefa antes de perguntar qualquer coisa, leio o que você já respondeu nos documentos e não repito pergunta; pergunto uma coisa de cada vez aqui no Maestro, você responde ou anexa o arquivo aqui mesmo, e eu não repasso pedido por WhatsApp nem por e-mail; anexo que você mandar fica no Maestro. Antes de construir, eu peço sua autorização: mostro o que vou montar e só sigo depois do seu ok. Eu não invento composição de proposta, benefício, número ou regra: o que não tiver resposta fica registrado como pendência.
  > Teste humano: eu monto um roteiro passo a passo para você conferir no app, com o nome dos campos e botões exatamente como aparecem na tela — se eu não tiver conferido um rótulo, eu descrevo a ação pelo resultado e não invento o nome do botão. O roteiro diz o que fazer, o que deve aparecer como resultado esperado e o que fazer se não aparecer. Envio uma tarefa por vez e espero o seu aceite antes de seguir.
  > Depende de: T-F3-002 (conteúdo piloto aprovado). Esta é a primeira tarefa que o vendedor usa no dia a dia.
- [ ] Revisar a proposta por versão e voltar para a anterior @Fábio !12/11/2026  <!-- id:f3a1c2d4-0004-4a01-9a01-000000000004 -->
  > Task T-F3-004. O que já existe: o rascunho de proposta da tarefa anterior. O que vai existir no Skip: toda vez que a proposta for editada, ela ganha uma versão nova e a anterior fica guardada marcada com qual versão a substituiu; o vendedor precisa revisar antes de enviar; e dá para voltar para a última versão aprovada, com registro do que foi feito e de quem fez.
  > Por que é útil: sem isso, duas pessoas editam o mesmo documento e ninguém sabe qual foi enviado ao cliente.
  > O que falta saber com você: quem pode editar, quem revisa e quem pode voltar uma versão — só as alçadas que ainda não estiverem definidas. Como a conversa funciona: eu digo a fase e a tarefa antes de perguntar, leio os documentos e as respostas anteriores, não repito pergunta e digo se a informação já existe; pergunto uma coisa de cada vez no Maestro e o anexo vem aqui; antes de mexer no app eu peço sua autorização e espero o seu ok; não invento regra de aprovação nem texto de aviso.
  > Teste humano: roteiro passo a passo no app, com rótulos conferidos na tela, mostrando como editar, como ver que nasceu uma versão nova, como a anterior aparece como substituída, como voltar para a anterior e o que deve aparecer em cada etapa. Um teste por vez, com o seu aceite antes do próximo.
  > Depende de: T-F3-003 (a proposta precisa existir em rascunho para ser revisada e versionada).
- [ ] Montar o folder da Fase 3 com os blocos aprovados @Fábio !17/11/2026  <!-- id:f3a1c2d4-0005-4a01-9a01-000000000005 -->
  > Task T-F3-005. O que já existe: os blocos de conteúdo aprovados na tarefa anterior e o material que você já enviou para o folder. O que vai existir no Skip: o folder gerado a partir desses blocos, com linhas, produtos e alegações só quando a fonte está válida e dentro da vigência, e a versão do folder registrada.
  > Por que é útil: o folder deixa de ser montado do zero e deixa de sair com informação vencida ou sem fonte.
  > O que falta saber com você: eu procuro primeiro o material do folder que você já enviou e peço só o que faltar — linhas e produtos que devem aparecer, alegações que a empresa pode fazer e qual é a fonte aprovada de cada uma. Se não houver fonte aprovada, aquilo vira pendência com nome e dono, nunca entra no folder em silêncio.
  > Como funciona a conversa: fase e tarefa ditas antes de perguntar, leitura do que já foi respondido para não repetir pedido, uma pergunta de cada vez no Maestro, anexos aqui, nenhum envio ao Ramon. Antes de construir, peço sua autorização. Não invento composição do folder, benefício nem fonte.
  > Teste humano: roteiro passo a passo com os rótulos reais da tela, resultado esperado de cada passo e o que conferir no folder gerado. Uma tarefa por vez, aguardando o seu aceite.
  > Depende de: T-F3-002 (os blocos de conteúdo já aprovados). Pode ser feita em paralelo com a montagem da proposta.
- [ ] Enviar proposta e folder com registro e recuperação @Fábio !19/11/2026  <!-- id:f3a1c2d4-0006-4a01-9a01-000000000006 -->
  > Task T-F3-006. O que já existe e está decidido: o canal de envio é o WhatsApp, com 3 vendedores e 1 número para cada um — isso não será perguntado de novo. O que vai existir no Skip: o envio de proposta e folder com registro de quem enviou, para quem, quando e qual versão foi, e a recuperação quando algo falhar, sem perder a tentativa.
  > Por que é útil: hoje o envio não deixa rastro e, quando falha, não se sabe se o cliente recebeu.
  > O que decidir com você antes de eu construir: a forma de entrega. Opção A, manual: o vendedor baixa o arquivo ou o link e envia pelo WhatsApp, e o sistema só registra o ato — simples, sem custo novo. Opção B, API oficial do WhatsApp: o sistema envia sozinho, com custo por mensagem, credenciais e aprovação da Meta. Eu explico o que cada uma exige e você autoriza antes de qualquer construção; nada é ativado por conta própria.
  > Atenção a uma diferença: o conector do Maestro, que é o meu, não é a integração do Skip — ter um não dá o outro. Qualquer API, custo ou credencial nova passa pelo consultor antes, e não ligo nada sem esse aval.
  > Como funciona a conversa: digo a fase e a tarefa antes de perguntar, leio o que já está decidido e não repito, uma pergunta de cada vez no Maestro, anexos aqui. Não invento formato de arquivo, texto de mensagem nem regra de reenvio.
  > Teste humano: roteiro passo a passo com rótulos conferidos na tela, incluindo o envio de uma versão não aprovada, que deve ser barrado, e a recuperação depois de uma falha, com o resultado esperado de cada passo. Uma tarefa por vez, com aceite antes de seguir.
  > Depende de: T-F3-003 e T-F3-005 (só se envia o que já foi montado e revisado).
- [ ] Garantir permissão, auditoria e bloqueio por padrão @Fábio !24/11/2026  <!-- id:f3a1c2d4-0007-4a01-9a01-000000000007 -->
  > Task T-F3-007. O que já existe: o gestor e o administrador que já aprovam o conteúdo fechado; eles continuam sendo quem aprova. O que vai existir no Skip: registro de cada ação em conteúdo, proposta e folder (quem fez, com qual papel, o que mudou, por quê, qual versão e se deu certo), o vendedor barrado com motivo visível e, quando a permissão de uma ação não estiver definida, a ação é negada por padrão em vez de passar.
  > Por que é útil: é o que sustenta a confiança no documento enviado ao cliente e mostra quem liberou o quê.
  > O que falta saber com você: só as alçadas que ainda não estão definidas — quem pode enviar proposta, quem pode reenviar e quem aprova cada tipo de conteúdo, se for diferente do gestor e do administrador já definidos.
  > Como funciona a conversa: fase e tarefa ditas antes de perguntar, leitura do que já foi respondido para não repetir, uma pergunta de cada vez no Maestro, anexos aqui. Não invento alçada nem regra de permissão.
  > Teste humano: roteiro passo a passo com os rótulos reais, mostrando o vendedor sendo barrado e o motivo aparecendo, o registro da ação e o bloqueio quando a permissão não existe, com resultado esperado por passo. Uma tarefa por vez, com aceite antes da próxima.
  > Depende de: T-F3-006 (as permissões cobrem o que o envio já faz).
- [ ] Demonstrar o fluxo completo com um caso real e fechar a Fase 3 @Fábio !26/11/2026  <!-- id:f3a1c2d4-0008-4a01-9a01-000000000008 -->
  > Task T-F3-008. O que vai existir: uma demonstração do começo ao fim com um caso real — o pedido entra, a proposta é montada, o vendedor revisa, o envio fica registrado e o folder sai com os blocos aprovados; junto vêm a lista de pendências de conteúdo com dono e motivo e a exportação da trilha em planilha.
  > Por que é útil: é a prova de que o processo funciona inteiro antes de liberar a fase seguinte.
  > Regra do ensaio: nada é disparado para cliente real sem a sua confirmação na hora. A demonstração usa o caso real escolhido por você, e o envio só acontece depois do seu ok explícito; sem ok, o ensaio para antes do envio.
  > O que falta saber com você: qual caso real serve de demonstração e quem do time acompanha. Como a conversa funciona: fase e tarefa ditas antes de perguntar, leitura do que já está respondido, uma pergunta de cada vez no Maestro, anexos aqui. Não invento passo, resultado nem número.
  > Teste humano: roteiro completo passo a passo, com rótulos conferidos na tela e resultado esperado em cada etapa, terminando no aceite do responsável pelo projeto. Uma etapa por vez, aguardando seu aceite.
  > Depende de: T-F3-007 (o ensaio completo só faz sentido com permissão e auditoria no lugar).
- [ ] Revisar as evidências e decidir o fechamento da Fase 3 @"Ramon Pádua" !30/11/2026 #aculturamento  <!-- id:f3a1c2d4-0009-4a01-9a01-000000000009 -->
  > Revisão do consultor. Só o consultor fecha esta tarefa: conferir as evidências e os aceites das 8 tarefas da fase, confirmar as decisões herdadas do encerramento da Fase 2 e decidir se a Fase 3 está fechada, com o registro do que passa para a Fase 4.
  > Nenhuma tarefa desta fase é dada como concluída por aceite de tela sem prova: cada uma precisa do registro do teste humano correspondente. Esta tarefa não abre conversa com o cliente nem com o Fábio e não pede nada no Maestro a ele: é decisão do consultor, uma só, que autoriza ou não o fechamento e registra o motivo por escrito. O cliente não vê esta tarefa.
  > Depende de: todas as anteriores desta fase.
