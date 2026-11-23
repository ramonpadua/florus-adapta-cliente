# Fase 3 — Tarefas

<!-- fase-format:2 -->

## Ondas

### Onda 5 — Folder

- [x] Montar o folder da Fase 3 com os blocos aprovados @Fábio !17/11/2026  <!-- id:f3a1c2d4-0005-4a01-9a01-000000000005 -->
  > Task T-F3-005. O que já existe: os blocos de conteúdo aprovados na tarefa anterior e o material que você já enviou para o folder. O que vai existir no Skip: o folder gerado a partir desses blocos, com linhas, produtos e alegações só quando a fonte está válida e dentro da vigência, e a versão do folder registrada.
  > Por que é útil: o folder deixa de ser montado do zero e deixa de sair com informação vencida ou sem fonte.
  > O que falta saber com você: eu procuro primeiro o material do folder que você já enviou e peço só o que faltar — linhas e produtos que devem aparecer, alegações que a empresa pode fazer e qual é a fonte aprovada de cada uma. Se não houver fonte aprovada, aquilo vira pendência com nome e dono, nunca entra no folder em silêncio.
  > Como funciona a conversa: fase e tarefa ditas antes de perguntar, leitura do que já foi respondido para não repetir pedido, uma pergunta de cada vez no Maestro, anexos aqui, nenhum envio ao Ramon. Antes de construir, peço sua autorização. Não invento composição do folder, benefício nem fonte.
  > Teste humano: roteiro passo a passo com os rótulos reais da tela, resultado esperado de cada passo e o que conferir no folder gerado. Uma tarefa por vez, aguardando o seu aceite.
  > Depende de: T-F3-002 (os blocos de conteúdo já aprovados). Pode ser feita em paralelo com a montagem da proposta.
  > **Concluída em 2026-11-23:** aceite do champion ("Acabei de checar e tudo está conforme você descreveu") sobre roteiro de 8 passos. Parte técnica entregue em v0.0.200: coleção `folders` (migration 0040) com RLS, versão, linhagem e retenção; hook `montagem_folder.js` montando no servidor a partir dos blocos `aprovado` com fonte e vigência e do catálogo aprovado, com pendência nomeada e dono para o que não entra; revisão com motivo, nova versão e rollback com recibo na auditoria; tela no menu. Evidência: TDD 25/25 PASS + ciclo do rollback 21/21 PASS + revalidação de conclusão 15/15 PASS (CA-3-012…016) + QA OK v0.0.200. **O que NÃO está pronto e não depende de mim:** os 5 blocos de conteúdo do folder estão vazios e sem fonte (pendência do champion) — por isso o folder monta, mostra os 107 produtos e as 5 pendências com dono, mas não pode ser aprovado.
