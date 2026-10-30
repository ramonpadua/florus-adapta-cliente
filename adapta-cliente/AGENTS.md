# AGENTS.md

Instruções para agentes de código neste repositório. **A fonte de verdade é o
[`CLAUDE.md`](CLAUDE.md)** — leia-o antes de agir. Em resumo:

1. Este é o repositório do projeto de um cliente da consultoria Adapta. O trabalho avança
   **uma fase por vez** (`04_fase-atual/`); o estado vive em `STATUS.md`.
2. Não edite specs nem o plano — dúvidas viram registro no `changelog.md` para o consultor.
3. Task só fecha com o critério de pronto binário cumprido, com evidência.
4. Prefira `/adapta-cliente:trabalhar` para abrir a próxima task e
   `/adapta-cliente:finalizar-task` para validar/fechar. Se houver erro tecnico, use
   `/adapta-cliente:destravar-task` antes de tentar concluir.
5. Toda ação relevante → `changelog.md`; progresso → `STATUS.md`.
6. Conteúdo confidencial do projeto; tudo em português.

## Protocolo de interação com o champion (tasks abertas da fase)

1. **Leia `04_fase-atual/fase.md` antes de qualquer coisa.** O texto da task aberta é o contrato: diz o
   que já existe, o que vai existir no Skip, por que é útil, o que perguntar e como provar.
2. **Explique antes de perguntar**, nesta ordem: o que já existe (e não deve ser pedido de novo) → o que
   vai existir no Skip → por que é útil. Só então faça a pergunta.
3. **Uma pergunta por vez, no chat do Maestro, e só sobre o que falta.** Leia os documentos e as
   respostas anteriores antes; não repita pergunta já respondida, não junte duas perguntas e não repasse
   pedido por WhatsApp nem por e-mail. Anexo enviado pelo champion fica no Maestro.
4. **Confira a fonte e peça autorização antes de construir.** Mostre o que vai montar e espere o "ok".
   Informação sem fonte aprovada vira pendência com nome e dono — nunca é preenchida no silêncio, e
   nenhuma regra, número, benefício ou nome de botão é inventado.
5. **Prove com roteiro humano:** passo a passo com os rótulos exatamente como aparecem na tela e o
   resultado esperado de cada passo; uma task por vez, com o aceite do champion antes da próxima.
6. **Nada fora da fase atual:** não altere as Fases 1 e 2, não detalhe fase futura e não envie nada a
   cliente real sem confirmação na hora.
