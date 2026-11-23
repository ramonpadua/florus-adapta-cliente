# T-F3-007 — Permissão, auditoria e bloqueio por padrão

**Versão:** v0.0.206 · **Data:** 23/11/2026 · **Etapa:** aguardando teste humano

## O defeito real que a análise encontrou

Quando alguém editava um pedido em um campo fora da lista de "sensíveis" (telefone, e-mail,
CEP, nome do projeto, nome fantasia), a auditoria gravava um evento com:

- ação `editar`
- **campo vazio**
- **antes e depois vazios**

Resultado: **50 eventos** no banco dizendo apenas "alguém editou este pedido". O registro não
respondia "quem mudou o quê" — que é exatamente o que a task promete. Pior: edição **sem**
nenhuma mudança relevante também gravava uma linha vazia, poluindo a trilha.

**Correção:** a lista de campos auditados passou de 9 para 44, cobrindo todos os campos de
negócio do pedido; e, se nada mudou de fato, **nada é gravado**.

## O que foi construído

- **Tela "Trilha"** no menu (gestor/admin): mostra quem fez, com qual papel, o que aconteceu,
  o campo, o valor **de → para**, o motivo, a versão e o resultado. Filtros por superfície
  (Pedidos, Conteúdo, Proposta, Folder, Envio, Catálogo, Preço, Investimento), período, quem/pedido,
  e um botão "Só ações negadas".
- **Exportação CSV sanitizada:** sem nome de pessoa, com o ID do ator — padrão aprovado pelo
  champion em 23/10 (accountability por ID, sem espalhar dado pessoal em planilha).
- **Correção da auditoria de pedidos** (acima).

## Prova

| Critério | Prova | Resultado |
|---|---|---|
| CA-3-023 | edição de campo antes não auditado registra o campo e o de/para | PASS (`contato_celular`, `nome_projeto`) |
| CA-3-024 | PATCH sem mudança real não cria evento | PASS |
| CA-3-024 | nenhum evento `editar` vazio **desde a correção** | PASS |
| CA-3-025 | evento em cada superfície da Fase 3 (proposta, folder, envio, conteúdo) | PASS (6/6) |
| CA-3-026 | fail-closed: alçadas existem, negativas auditadas com motivo | PASS |
| CA-3-027 | toda alçada declara papel mínimo; hierarquia respeitada | PASS |
| CA-3-028 | CSV sem nome de pessoa, com ID | PASS |

`TDD 27/27 PASS` (`scripts/tf3007-tdd.py`). QA Skip OK na v0.0.206. Tela verificada no preview.

## O que já estava certo e NÃO foi refeito

Os 500 eventos examinados tinham todos autor, papel e resultado — zero evento órfão. Auditoria de
proposta, folder e envio já era nomeada por superfície. Não dupliquei superfície que funcionava.

## Limpeza

Pedidos de prova arquivados por ID ao final do TDD. Nenhum registro apagado.
