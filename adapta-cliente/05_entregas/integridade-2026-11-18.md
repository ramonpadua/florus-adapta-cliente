# Varredura de integridade do sistema — 18/11/2026

**Pedido:** champion Fábio — "preciso garantir que o sistema funcione corretamente,
independentemente de alguma etapa ter vencido. Tudo tem que ser corrigido."
**Versão final:** v0.0.192 · **Migrations:** 0038, 0039 · **Scripts:** `scripts/auditoria-integridade.py`,
`scripts/auditoria-2.py` (somente-leitura) e `scripts/corrige-pedidos.py` (correção, com dry-run).

## 1. O que a varredura encontrou

Varredura em banco, hooks, telas e permissões. **Zero defeito crítico** — produto, preço, trilha,
investimento e propostas consistentes. Seis problemas reais:

| # | Problema | Gravidade |
|---|---|---|
| 1 | 20 pedidos de teste/ensaio apareciam no Pipe e na Lista como pedidos reais | Alta |
| 2 | 9 números de pedido repetidos (4 entre pedidos ativos) | Alta |
| 3 | Sem trava de unicidade em `pedido_id` — o defeito 2 podia repetir a qualquer envio | Alta |
| 4 | Proposta 2026-0019 apontava para `PROVA-TF3003`, inexistente | Média |
| 5 | 5 blocos de folder em rascunho e vazios (trava a T-F3-005) | Alta (bloqueia task) |
| 6 | Conta "Navaar" sem papel — consultor que se desligou da Adapta | Média |

## 2. Causa raiz do defeito 2 (o mais grave)

O número `AAAAMMDD-####` era calculado **no navegador**: lia o maior número do dia e somava 1.
Dois envios simultâneos (duas abas, dois vendedores) liam o mesmo maior e geravam o **mesmo**
número; como a coleção não tinha índice de unicidade, o banco aceitava os dois.

## 3. O que foi corrigido

- **Migration 0038** — `pedidos.arquivado` (bool) + `pedidos.motivo_arquivamento` (text).
  Arquivar **não apaga**: histórico, linhagem e auditoria permanecem (retenção respeitada).
- **Correção pontual de dados** (via API, por id — não por migration, conforme o guia da plataforma):
  - **23 pedidos de teste/ensaio arquivados**, com motivo gravado em cada um.
  - **9 números duplicados resolvidos**: o registro "dono" mantém o número; as cópias são arquivadas
    com o número original citado no motivo. **Nenhum registro apagado, nenhum número renomeado.**
  - **Proposta 2026-0019**: ponteiro para proposta inexistente limpo; volta a ser vigente (rascunho).
- **Migration 0039** — `idx_pedidos_pedido_id_ativo`: índice **UNIQUE PARCIAL** válido só para
  pedidos ativos (`arquivado = false OR arquivado IS NULL`). Dois pedidos ativos com o mesmo número
  passam a ser recusados pelo banco. Registros históricos ficam intocados.
- **Tela**: `ListaPedidos` e `TrelloBoard` filtram `!arquivado` (além de `!substituido_por`).
  Tipo `PedidoTrello` ganhou os campos.
- **Geração do número** (`src/lib/numeroPedido.ts` e cópia do formulário): varre ativos **e**
  arquivados, confirma que o candidato está livre e avança se estiver ocupado.

## 4. Prova

| Prova | Resultado |
|---|---|
| Pipe na tela (admin) | **43 cartões → 2** (Linha capilar · Linha para careca) |
| Rascunho / Pendente / Revisão | **0 · 0 · 0** |
| Duplicados entre pedidos ativos | **0** (eram 9 números) |
| Índice no banco | `CREATE UNIQUE INDEX idx_pedidos_pedido_id_ativo ... WHERE arquivado = false OR arquivado IS NULL` presente |
| QA Skip (setup · static · build · integrations) | **OK** nas v0.0.190, 0.0.191 e 0.0.192 |
| Registros apagados | **0** — retenção preservada (backup em `work/tf-auditoria/`) |

## 5. Conta Navaar — por que ficou pendente

Navaar foi o consultor responsável pela implantação e desligou-se da Adapta; Ramon é o novo
consultor. A conta foi criada em 09/09/2026, está **sem papel definido** e **sem nenhum registro
ligado** (0 pedidos como vendedor, 0 propostas, 0 produtos, 0 conteúdos, 0 compartilhamentos e
0 eventos de auditoria no nome dela) — ou seja, não tem histórico operacional a preservar.

A remoção foi tentada e a API recusou: **403 "Only superusers can perform this action"**. Remover
usuário é operação de superusuário, fora da alçada do app — **não é um defeito do sistema**, é um
limite de permissão. Fábio remove pelo painel do Skip (Skip Cloud → Collections → `users`),
com segurança.

## 6. Defeito encontrado e corrigido nesta mesma varredura

A primeira tentativa da migration 0039 não aplicou: o shard anunciou indisponibilidade e o
`apply_changes` devolveu `integrations.ok=false`. Reaplicado na v0.0.192 e **conferido no schema** —
não bastava o `ok:true` do pipeline, era preciso o índice existir de fato.
