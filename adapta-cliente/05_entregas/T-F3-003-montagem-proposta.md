# T-F3-003 — Proposta montada a partir do pedido, catálogo e conteúdo aprovado

**Task:** T-F3-003 (SPEC-3-002, onda 3) · **Status:** CONCLUÍDA em 30/10/2026 · **App:** v0.0.185
**Aceite do champion:** *"Deu certo. Pode seguir."* + *"Sim, consigo editar os quatro campos"* (30/10, formulário)

## O que foi entregue

O vendedor abre o pedido e monta a proposta em um clique dentro do Skip:

- **Blocos de texto aprovados** preenchidos automaticamente (capa · escopo · investimento · condições · próximos passos; os 3 blocos ainda sem conteúdo aprovado aparecem como pendência nomeada);
- **Tabela de produtos** do pedido com preço vigente do catálogo, R$ por unidade, R$ produção, ANVISA (**uma vez por produto**, nunca por quantidade) e TOTAL;
- **Investimento com componentes separados** e totais conferidos contra o motor da Fase 2 — divergência vira pendência `revisao_de_preco` bloqueante, nunca silêncio;
- **Condições de pagamento** padrão 80% no aceite / 20% no término da produção antes da saída das mercadorias (modificação do champion em 29/10), alteração só com aprovação de gestor e ninguém aprova a própria alteração;
- **Validade de 15 dias corridos** e **snapshot congelado** (edição gera nova versão — T-F3-004);
- **Amostras aprovadas pelo cliente** como CONTEÚDO da proposta: tabela no documento (Aprovada · ID · Descrição · Lote · Validade) **no lugar do marcador**, pré-preenchida com ID e descrição dos produtos da proposta, **editável na própria tabela**, com pendência bloqueante enquanto faltar ID, descrição, lote ou validade;
- **Impressão pelo navegador** (Cmd+P → Salvar como PDF), sem ferramenta externa.

Estados: nasce `rascunho`; envio negado até a revisão (RN-3.005/CA-3-008); envio com registro é da SPEC-3-004.

## Critérios de aceite — verificação independente de encerramento

| Critério | Prova observável | Veredito |
|---|---|---|
| **CA-3-007** proposta gerada do pedido com blocos aprovados, produtos vigentes e investimento com componentes | pedido vinculado; 1 bloco por conteúdo aprovado (capa/escopo/investimento/condições/próximos passos) com `conteudo_id` + título + corpo; tabela do pedido com preço vigente (R$ 9,59/un); ANVISA 1× por produto (R$ 450); total da linha = produção + ANVISA (19.178 + 450 = 19.628); totais conferem; condições 80/20; validade 15; `proposta_id` no padrão `2026-####` | **PASSOU** |
| **CA-3-008** nasce `rascunho` e não envia antes da revisão | nasce `rascunho`; envio negado (HTTP 400) com *"Proposta em rascunho NUNCA envia: a revisão do vendedor é obrigatória antes (RN-3.005 / CA-3-008)"*; estado preservado em `rascunho` após a negativa | **PASSOU** |
| **CA-3-010** divergência gera pendência nomeada, nunca silêncio | pendências com código + rótulo + dono + `bloqueia_envio`; revisão barrada citando o código; pedido inexistente recusado sem criar registro | **PASSOU** |
| RLS por vendedor | vendedor lê apenas as próprias propostas | **PASSOU** |
| Snapshot imutável | alteração do documento congelado negada | **PASSOU** |
| Regressão Fases 1 e 2 | 57 pedidos legíveis; 107 produtos aprovados; 5 blocos aprovados; 200 investimentos legíveis; I6 não tocada | **PASSOU** |

**Totais:** TDD `scripts/tf3003-tdd.py` **56/56 PASS** do zero · critérios de aceite **21/21 PASS** · QA Skip OK (setup/static/build/integrations) na **v0.0.185**.

## Correções feitas durante o teste (todas apontadas pelo champion, todas corretas)

| # | O que o champion viu | Causa real | Correção |
|---|---|---|---|
| 1 | "não tem botão de Imprimir / Alterar condições" | botões existiam, espremidos na linha do título da seção | **v0.0.178–179**: bloco próprio "Ações da proposta ANO-####" com os três botões e legenda + segunda porta de impressão junto do documento |
| 2 | "não vi os dados das amostras — são fundamentais e condição do aceite" | tratei amostra como fase posterior; **erro meu** | **v0.0.180–183**: campo `amostras` (migration 0036), pendência bloqueante, tabela no documento, botão de registro |
| 3 | "`{{amostras_aprovadas}}` aparece na tela e a tabela cai depois da assinatura" | marcador não consumido pelo render | **v0.0.184**: tabela renderizada **no lugar do marcador** + pré-preenchimento por ID/descrição |
| 4 | "não consegui mudar ID, Descrição, Lote e Validade" | grade editável existia em bloco separado; a tabela do documento era só leitura | **v0.0.185**: a própria tabela do documento entra em modo de edição (+Adicionar / Salvar / Cancelar abaixo dela) |

Padrão que se repetiu e virou regra: **"não achei" = está em outro lugar**. O recurso certo no lugar errado é defeito.

## Observações herdadas (não bloqueiam a task)

- **I6 (política de faturamento mínimo) ausente:** todo pedido com investimento calculado cai em `revisao_de_preco`; propostas montam e são revisadas, mas o envio depende da política — pendência herdada da Fase 2.
- **3 blocos sem conteúdo aprovado** (`entendimento`, `materias_primas`, `procedimentos`): aparecem como `conteudo_ausente:*` nomeados; aguardam Marketing/Regulatório.
- **Visual da proposta** será refeito em trabalho próprio (`06_notas/insumo-visual-proposta-2026-10-30.md`); a ordem dos blocos é dado aprovado — mudar exige nova versão de conteúdo.
- **Defeito de prova corrigido:** o script de TDD marcava como substituída **toda** proposta vigente do pedido de prova e atingiu a 2026-0017 montada pelo champion no teste humano. Restaurada; o script passou a limpar apenas o que ele mesmo cria (identificado pelo motivo de criação).

## Registro para o changelog (entradas desta task)

- **T-F3-003 concluída (30/10):** proposta montada a partir do pedido, catálogo aprovado e motor de investimento, com snapshot congelado, condições 80/20, validade 15 dias e amostras aprovadas. Aceite na 4ª rodada. Evidência: TDD 56/56 PASS + critérios CA-3-007/008/010 21/21 PASS + QA Skip OK na v0.0.185.
- **v0.0.184:** marcador `{{amostras_aprovadas}}` visível e tabela depois da assinatura → render no lugar da âncora + pré-preenchimento por ID/descrição dos produtos.
- **v0.0.185:** campos de amostra não editáveis na tabela do documento → a própria tabela entra em modo de edição; TDD 12/12 + digitação real na tela.
- **Correção de prova:** script de TDD limpava toda proposta vigente do pedido de prova (atingiu a 2026-0017 do champion, restaurada); agora limpa só o que ele mesmo cria.

## Arquivos atualizados no fechamento

`04_fase-atual/fase.md` (checkbox e conclusão) · `STATUS.md` (3/9 · 33%) · `.adapta-cliente/estado-atual.md` · este relatório.
