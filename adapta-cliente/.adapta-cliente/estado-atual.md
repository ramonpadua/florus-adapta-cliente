# Estado atual — Adapta Cliente

- task_id: T-F3-004
- champion: Fábio
- spec: 04_fase-atual/specs/spec-3-002-montagem-proposta.md
- etapa: concluida
- autorizacao_implementacao: confirmada (2026-11-02 20:47)
- teste_humano: aprovado em 2026-11-10 ("Aparentemente, tudo está da maneira como você descreveu", com imagem da tela) após 2 rodadas; 1ª rodada reprovou 3 pontos de tela, corrigidos na v0.0.189
- verificacao_automatica: TDD 32/32 PASS + QA Skip OK na v0.0.189
- aprendizado: capturado — SOUL.md (histórico de linhagem filtra por raiz; bloco que cresce esconde o resto) + changelog
- ultima_acao: T-F3-004 concluída; STATUS 4/9 (44%); fase.md marcado [x]
- proxima_acao: T-F3-005 (folder da Fase 3) — pedir autorização ao champion antes de analisar/implementar
- atualizado_em: 2026-11-10T18:05:00-03:00

---
## Teste humano T-F3-004 — 1ª rodada (10/11) e o que mudou na v0.0.189

O champion apontou três pontos; **os três eram defeito de TELA, não de lógica**:

1. **"Não achei 'Ações da proposta'."** O bloco existia, mas ficava **abaixo** de uma tabela de 25
   linhas e saía da área visível.
2. **"Não achei a tabela 'Histórico desta proposta'."** A tabela existia — mas listava as propostas
   de **todas as linhagens do pedido** de prova, não a da proposta aberta (25 linhas, incluindo
   linhagens de execuções anteriores do TDD e as marcadas `LIMPEZA-PROVA-TF3004`).
3. **"Só aparecem 2 versões no seletor: V1 e V3."** Listava só as vigentes; a v2 substituída sumia.

Correções na v0.0.189: histórico por **raiz de linhagem** (`proposta_origem`) com a versão aberta
marcada "← aberta"; seletor com a **linhagem inteira** marcando as substituídas; abrir substituída
entra em **modo consulta** (aviso + sem botões que escrevem); tabela com altura máxima e rolagem.

**2ª rodada: APROVADA em 10/11** com imagem da tela confirmando os três blocos. TDD 32/32 PASS.

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
## Decisões do champion — T-F3-004 (02/11)
- Quem cria nova versão / edita: **vendedor dono + gestor/admin**.
- Quem marca como revisada: **vendedor dono + gestor/admin**.
- Quem volta para a última versão: **gestor/admin**.
- O que a versão nova traz: **remontar com o pedido e o catálogo do momento**.

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
