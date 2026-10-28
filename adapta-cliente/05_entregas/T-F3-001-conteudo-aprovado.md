# T-F3-001 — Conteúdo aprovado: proposta e folder versionados

**Task:** T-F3-001 (SPEC-3-001, onda 1) · **Fase:** 3 · **Versão:** v0.0.165–171
**Autorização:** champion Fábio, 2026-10-28 (formulário "Enviar respostas": "Sim, implementar o plano")
**Prova automática:** TDD CP-F3-001/002/003 — **32/32 PASS** (`scripts/tf3001-tdd.py`)
**Versão final:** v0.0.171 (emenda de usabilidade autorizada pelo champion em 28/10)

## Decisões do champion nesta task (insumos embutidos)

| Ponto | Decisão registrada |
|---|---|
| Blocos da proposta | **6 blocos** — capa · entendimento da necessidade · escopo do projeto · investimento e composição de custos · condições comerciais e validade · próximos passos |
| Blocos do folder | **5 blocos** — capa/identidade Florus · linhas de produto · produtos em destaque · alegações aprovadas · contato e rodapé |
| Quem aprova | **gestor/admin aprova todos os blocos** (mesma regra do catálogo da Fase 2); dono do texto registrado no bloco |
| Fonte oficial | **Três fontes declaradas por bloco** — documentos comerciais vigentes da Florus · catálogo governado (produtos aprovados) · Regulatório/ANVISA (alegações) |
| Vigência | **31/12/2026** como padrão, editável por bloco |

## O que foi construído

1. **Coleção `conteudos`** (migration 0032): `conteudo_id` único (`PROP-0001` / `FOLD-0001`), tipo (proposta/folder), bloco, título, corpo, fonte, `vigencia_fim`, status (rascunho / pendente_aprovacao / aprovado / substituido), pendências nomeadas, versão, `substituido_por`, `versao_anterior`, ordem, responsável.
   **RLS:** vendedor lê **somente aprovado**; gestor/admin leem e escrevem tudo; **exclusão bloqueada** (`deleteRule: null` — retenção).
2. **Semente (migration 0033):** os 11 blocos da estrutura aprovada, criados como rascunho sem texto, cada um já com pendência nomeada — invisíveis ao vendedor até receberem texto + fonte + aprovação. O conteúdo real é a T-F3-002.
3. **Hook `validacao_conteudos.js`** (JSVM, lógica inline, um callback por coleção):
   - RN-3.001 — título/corpo ausente → `pendente_aprovacao` + `campo_minimo_ausente:*`; nunca aprovado incompleto.
   - RN-3.002 — sem fonte ou vigência vencida → `fonte_nao_declarada` / `vigencia_vencida`; a versão aprovada anterior segue válida.
   - RN-3.003 — duplicidade tipo+bloco **bloqueada** com mensagem que aponta a versão vigente; bloco aprovado **não é editado no lugar** (alteração = nova versão); nova versão aprovada marca a anterior `substituido` + `substituido_por`.
   - RN-3.004 — exclusão bloqueada e auditada.
   - Auditoria nomeada: `criar_conteudo`, `substituir_conteudo`, `aprovar_conteudo`, `editar_conteudo`, negativas de duplicidade/edição/exclusão.
   - Aprovação exige **motivo ≥ 10 caracteres**.
4. **Biblioteca `src/lib/conteudos.ts`** — espelha as regras no cliente (pendências, status derivado, visibilidade ao vendedor, estrutura dos 11 blocos, três fontes, vigência padrão).
5. **Tela “Conteúdos”** (`src/components/Conteudos.tsx` + botão no cabeçalho): alterna proposta/folder, mostra status, versão, `conteudo_id`, fonte, vigência, responsável e pendências nomeadas; botões coerentes com o estado (Editar só em não-aprovado; Nova versão só em aprovado; Aprovar só quando não há pendência) e Histórico de versões por bloco.

## Provas (TDD CP-F3-001/002/003 — 32/32 PASS)

| Bloco | Prova | Resultado |
|---|---|---|
| Estrutura | 6 blocos de proposta + 5 de folder semeados, `conteudo_id` único com prefixo | PASS |
| CA-3-001 | cadastro gera `conteudo_id` (PROP-0015), nasce rascunho, versão 1; aprovação sem motivo → 400; com motivo → aprovado | PASS |
| CA-3-002 | corpo ausente → `pendente_aprovacao` + `campo_minimo_ausente:corpo`; **invisível ao vendedor** (totalItems 0) | PASS |
| CA-3-003 | sem fonte → `fonte_nao_declarada`; vigência no passado → `vigencia_vencida`; ambos ficam pendentes | PASS |
| Aprovação | bloco com pendência não pode ser aprovado (400 nomeando a pendência) | PASS |
| RLS | vendedor recebe só `aprovado` (todos aprovados); não cria (400) nem edita (404) | PASS |
| CA-3-005 | duplicidade tipo+bloco bloqueada nomeando a versão vigente; nova versão v2 aprovada → anterior `substituido` com `substituido_por`; **1 versão vigente** | PASS |
| RN-3.004 | exclusão física negada (403) | PASS |
| Auditoria | eventos `criar_conteudo`, `aprovar_conteudo`, `substituir_conteudo` + duplicidade negada com motivo | PASS |

**Achado real corrigido no 1º run:** quando a nova versão já nascia aprovada no create (“Salvar e aprovar”), a versão anterior **não** era marcada como substituída — duas versões ficariam vigentes (violação do RN-3.003). Corrigido no hook (v0.0.166) e provado: 1 versão vigente por bloco. Registro: `06_notas/aprendizado-continuo/AP-2026-10-28-1600-estado-aprovado-no-create.md`.

**Prova na tela:**
- Admin (`fabio@florus.com.br`): tela Conteúdos com os 6 blocos da proposta, pendências nomeadas, fonte/vigência/responsável e botões coerentes com o estado.
- Vendedor (`vendedor@teste.florus.com.br`): vê **somente** a Capa aprovada; nenhum bloco pendente aparece e não há botão de editar/aprovar.

## Emenda de usabilidade (28/10, autorizada pelo champion)

O teste humano passou ("Concluí o teste e tudo deu certo") com duas considerações, ambas atendidas:

| Consideração | Causa real | Correção (v0.0.170–171) |
|---|---|---|
| "Não achei nenhuma mensagem de Bloco Aprovado" | O toast existe, mas nasce no canto inferior direito da janela, longe do bloco em que se clicou | Faixa de confirmação **na própria linha do bloco** (verde sucesso / vermelha erro, com botão fechar), mantendo o toast como reforço |
| "Não achei selo verde em nenhum lugar" | Não era defeito do teste: o selo herdava `--primary` do tema, que é quase preto | Selo com **cor semântica**: verde "✓ aprovado" · âmbar "pendente" · cinza rascunho/substituído |

**Achado na prova da própria emenda (v0.0.171):** a faixa dizia "✓ Bloco aprovado" também quando a ação era apenas salvar rascunho. Corrigido: o título agora é "Rascunho salvo" ou "Bloco aprovado" conforme a ação real. Provado na tela.

**Limpeza e estado final do banco:** os blocos de prova do TDD ficaram como `substituido` (retenção — nada apagado) e o texto de prova do bloco Escopo foi removido. Estado final: **só `PROP-0019` (Capa) aprovado**; o vendedor vê exatamente 1 item.

## Pendências e limites

- **Conteúdo real (texto da proposta e do folder) ainda não existe no sistema** — é o escopo da T-F3-002; hoje os demais 10 blocos seguem “sem texto (bloco pendente)”.
- Fonte é campo governado por seleção das três fontes aprovadas (sem alterar a coleção `dominios` da Fase 2, como previsto no plano).
- Nada das coleções das Fases 1–2 foi alterado; Cotação e campos travados do pedido intactos.

## Gate

**Teste humano APROVADO em 2026-10-28** ("Concluí o teste e tudo deu certo, com 2 considerações") — as 2 considerações foram atendidas na v0.0.170–171 e a task foi **concluída** em 28/10 (fase.md [x]; STATUS 1/9; changelog v24).
