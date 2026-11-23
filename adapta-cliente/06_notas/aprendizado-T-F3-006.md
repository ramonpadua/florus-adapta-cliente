# Aprendizado — T-F3-006 (envio registrado)

## O que o champion ensinou (vale para todo o projeto)

1. **Um campo por ATO REAL do vendedor, não por controle que o gestor quer ver.** Eu propus dois
   marcos (recebimento e entendimento) e ele recusou: acontecem na mesma ligação. Criar dois
   registros para uma conversa única gera campo que o vendedor preenche de qualquer jeito. A régua
   dele: *"não enchermos o sistema de processos e acabar atrapalhando a vida do vendedor"*.
2. **O registro precisa custar algo para valer.** Exigir "com quem do lado do cliente falou" é o
   que transforma o clique em conversa. Sem isso o campo é teatro.
3. **Rastreabilidade ≠ certificação.** O sistema registra a declaração do vendedor com autor, data
   e versão. Não prova entrega nem entendimento — e dizer isso ao champion evita expectativa falsa.

## Erros meus neste ciclo

- **`documento_versao` gravava 0.** Usei `getString` num campo `number` do PocketBase: devolve
  vazio, o fallback preencheu zero, e o envio diria "versão 0" para sempre. Só o TDD pegou.
  **Regra:** campo `number` lê-se com `getInt`/`getFloat`; `getString` numérico falha em silêncio.
- **Falso positivo ao investigar.** Conferi a versão da proposta 2026-0075 como v1 e concluí que
  havia defeito na numeração. Não havia: 2026-0075 era uma proposta NOVA de linhagem própria, e o
  envio gravou v1 corretamente. Quase reportei defeito inexistente ao champion.
  **Regra:** antes de declarar defeito, reproduzir o caminho exato — e distinguir "versão da
  linhagem" de "proposta nova".
- **Criei um registro de prova no banco de demonstração** (2026-0076) ao reproduzir, e tive de
  marcá-lo como substituído e restaurar a 2026-0074. Reprodução de defeito merece ambiente
  descartável, não a tela que o champion vai abrir.

## Cenário de prova sem forjar estado

O caminho positivo foi produzido pelo motor real: pedido com produto do catálogo → montagem →
nova versão com amostras → revisão. Se o motor não produzisse uma proposta revisável, o teste
falharia em vez de injetar `estado = 'revisada'` no banco. Esse é o padrão a repetir.
