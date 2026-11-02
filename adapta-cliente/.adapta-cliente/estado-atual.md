# Estado atual — Adapta Cliente

- task_id: nenhuma (T-F3-003 concluída em 2026-10-30)
- champion: Fábio
- spec: 04_fase-atual/specs/spec-3-002-montagem-proposta.md
- etapa: concluida
- autorizacao_implementacao: confirmada (2026-10-29 21:36, formulário; MODIFICAÇÃO: condições padrão 80% no aceite + 20% no término, alteração só com aprovação de gestor)
- teste_humano: aprovado (2026-10-30 21:39, formulário: "Sim, consigo editar os quatro campos" + "Deu certo. Pode seguir.")
- verificacao_automatica: passou (revalidação de encerramento do zero: TDD 56/56 PASS; critérios CA-3-007/008/010 21/21 PASS; QA Skip OK na v0.0.185)
- aprendizado: capturado:memory/SOUL.md (marcador {{...}} e "edite onde a pessoa olha") + changelog v30/v31 + relatório de entrega
- ultima_acao: T-F3-003 CONCLUÍDA — v0.0.185; fase.md [x]; STATUS 3/9 (33%); changelog com aceite e correção do script de prova; proposta de prova 2026-0026 limpa e a do champion (2026-0017) restaurada
- proxima_acao: aguardar decisão do champion sobre iniciar a T-F3-004 (revisão, versão e rollback da proposta)
- atualizado_em: 2026-10-30T21:45:00-03:00

---
## Teste humano T-F3-003 — 1ª rodada (30/10) e o que mudou

O champion **não concluiu** o teste e apontou três pontos:

1. **Ordem dos tópicos:** o sistema mostra escopo antes do investimento; a proposta 2026-0212
   trazia o investimento logo após a capa. Ele disse "não tem problemas manter assim, mas
   precisaremos refazer o visual da proposta". → **INSUMO**, não defeito: ordem é dado aprovado
   (T-F3-001/002); o visual será refeito em trabalho próprio. Registro em
   `06_notas/insumo-visual-proposta-2026-10-30.md`.
2. **"Não tem botão de Imprimir"** → o botão existia, mas espremido na linha do título da seção.
   Correção v0.0.178: bloco **"Ações da proposta ANO-####"** com borda, os três botões com rótulo
   completo e legenda do que cada um faz — mais uma **segunda porta de impressão junto do
   documento**.
3. **"Não tem botão de Alterar condições de pagamento"** → mesmo defeito de tela, mesma correção.
   A primeira tentativa duplicou o botão (dois iguais no painel); removido na v0.0.179.

Verificado na tela após o deploy: um botão de cada, visíveis em bloco próprio.

## Teste humano T-F3-003 — 2ª rodada (30/10): tela aprovada, AMOSTRAS recusadas

O champion confirmou que a tela "funcionou exatamente como eu previu" e decidiu **manter o visual
como está por enquanto**. Mas **recusou a entrega** com um ponto correto:

> "Não vi, na área de aprovação da proposta, os dados das amostras. Eles são fundamentais. [...] Não
> entendi o motivo de você desconsiderar a necessidade do cliente indicar na proposta quais amostras
> foram aprovadas, já que essa é uma condição da proposta ser aceita pela Florus. Por favor, explique
> sua decisão ou corrija antes de seguirmos."

**Ele está certo e eu errei:** tratei a tabela como dado da fase de amostras e deixei um espaço
reservado. Indicar as amostras aprovadas é CONTEÚDO da proposta e condição do aceite da Florus.
Correção v0.0.180–183 (ver changelog v29 e item 8 do relatório): campo `amostras`, pendência
bloqueante sem amostra, tabela no documento com as colunas do arquivo 2026-0212 e botão de registro.
TDD 10/10 PASS.

## Teste humano T-F3-003 — 3ª e 4ª rodadas (30/10): APROVADA

- **3ª rodada:** champion reportou que não conseguia mudar ID, descrição, lote e validade — a grade
  editável existia num bloco separado e a tabela do documento era só leitura. Correção v0.0.185: a
  própria tabela do documento entra em modo de edição (mesmo componente que exibe é o que edita).
- **Aceite (2026-10-30 21:39, formulário):** "Sim, consigo editar os quatro campos" + "Deu certo.
  Pode seguir." → **T-F3-003 CONCLUÍDA.**

---
## Fechamento da Fase 2 (26/10)
- Revisão do consultor (Ramon Pádua): 8/8 evidências e aceites conferidos; D1/D2 já aprovadas pelo champion (02/10); check-fase-2.md assinado.
- Evoluções adiadas: política de faturamento mínimo (I6) e exceção de piso POR PEDIDO.

---
## Decisões do champion — T-F3-003 (29/10)
- Formato: imprimir/salvar PDF pela própria tela · Validade: 15 dias corridos · Preço: snapshot congelado · Faltante: calcular na hora com pendência nomeada.
- **Condições de pagamento (MODIFICAÇÃO):** padrão **80% no aceite + 20% no término da produção, antes da saída das mercadorias**; vendedor pode alterar **desde que aprovado por gestor**; ninguém aprova a própria alteração.

---
## Decisões do champion — T-F3-002 (29/10)
- Proposta 2026-0212 é ponto de partida, não modelo final (obsoleta, visual ruim).
- Estrutura de 6 → 8 blocos (+ matérias-primas, + procedimentos).
- Atenção às células verdes (alertas) e às etapas da entrega.
- Cliente devolve a proposta assinada, com destaque para as amostras aprovadas.

---
## Decisões do champion — T-F3-001 (28/10)
- 6 blocos de proposta · 5 de folder · gestor/admin aprova todos · três fontes por bloco · vigência padrão 31/12/2026 editável · expansão só com novo aceite.

---
## Pendências herdadas da Fase 2
- Política de faturamento mínimo (I6): sem cadastro → investimento nasce em revisao_de_preco.
- Exceção de piso POR PEDIDO entra com a I6. Exceção da trilha = alteração com alçada do admin.

---
## Insumos do champion (T-F2-005)
- Componentes = formulação + Anvisa + serviços (Lacre 0,60 · Montagem caixa 2,50 · Rotulagem 0,60 · Celofane 1,00).
- ANVISA = UMA VEZ POR PRODUTO (20/10); serviços por unidade.
- I7 responsável por componentes e política = Fábio (admin).

---
## Decisões do champion (T-F2-002)
- Fonte oficial Tabela jan-2025 · migrar todas as linhas de uma vez · responsável produto/preço Fábio · vigência anual com item mandando · Industrialização e Serviços são parâmetros de custo.

---
## Histórico — T-F2-006 / T-F2-004 / T-F1
- T-F2-006 (23/10, v0.0.143–145) · T-F2-004 (19/10, 24/24 PASS) · T-F1-012, T-F1-009, T-F1-011, T-F1-010, T-F1-008, T-F1-007.
