# T-F3-001 — Conteúdo aprovado: proposta e folder versionados

**Task:** T-F3-001 (SPEC-3-001, onda 1) · **Fase:** 3 · **Versão:** v0.0.165–167
**Autorização:** champion Fábio, 2026-10-28 (formulário "Enviar respostas": "Sim, implementar o plano")
**Prova automática:** TDD CP-F3-001/002/003 — **32/32 PASS** (`scripts/tf3001-tdd.py`)

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
5. **Tela “Conteúdos”** (`src/components/Conteudos.tsx` + botão no cabeçalho): alterna proposta/folder, mostra status, versão, `conteudo_id`, fonte, vigência, responsável e pendências nomeadas; botões Editar / Nova versão / Aprovar (campo de motivo abre **na própria linha** — lição v0.0.163) e Histórico de versões por bloco.

## Provas (TDD CP-F3-001/002/003 — 32/32 PASS)

| Bloco | Prova | Resultado |
|---|---|---|
| Estrutura | 6 blocos de proposta + 5 de folder semeados, `conteudo_id` único com prefixo | PASS |
| CA-3-001 | cadastro gera `conteudo_id` (PROP-0015), nasce rascunho, versão 1; aprovação sem motivo → 400; com motivo → aprovado | PASS |
| CA-3-002 | corpo ausente → `pendente_aprovacao` + `campo_minimo_ausente:corpo`; **invisível ao vendedor** (totalItems 0) | PASS |
| CA-3-003 | sem fonte → `fonte_nao_declarada`; vigência no passado → `vigencia_vencida`; ambos ficam pendentes | PASS |
| Aprovação | bloco com pendência não pode ser aprovado (400 nomeando a pendência) | PASS |
| RLS | vendedor recebe só `aprovado` (2 itens, todos aprovados); não cria (400) nem edita (404) | PASS |
| CA-3-005 | duplicidade tipo+bloco bloqueada nomeando a versão vigente; nova versão v2 aprovada → anterior `substituido` com `substituido_por`; **1 versão vigente** | PASS |
| RN-3.004 | exclusão física negada (403) | PASS |
| Auditoria | eventos `criar_conteudo`, `aprovar_conteudo`, `substituir_conteudo` + duplicidade negada com motivo | PASS |

**Achado real corrigido no 1º run:** quando a nova versão já nascia aprovada no create (“Salvar e aprovar”), a versão anterior **não** era marcada como substituída — duas versões ficariam vigentes (violação do RN-3.003). Corrigido no hook (v0.0.166) e provado: 1 versão vigente por bloco.

**Prova na tela (preview v0.0.167):**
- Admin (`fabio@florus.com.br`): tela Conteúdos abre com os 6 blocos da proposta, badges de pendência nomeada, fonte/vigência/responsável e os botões Editar/Nova versão/Aprovar.
- Vendedor (`vendedor@teste.florus.com.br`): nenhum bloco pendente aparece; a tela informa “Nenhum texto aprovado para este bloco até o momento” (sem convite a cadastrar — ajuste de UX na v0.0.167).

## Registros de prova no banco

Blocos de prova criados durante o TDD (`PROP-0007…PROP-0018`, `FOLD-0006…FOLD-0009`) foram encerrados como `substituido` com motivo `PROVA-TF3001` — **nada foi apagado** (retenção). Os 11 blocos da estrutura seguem como rascunho pendente, prontos para a T-F3-002.

## Pendências e limites

- **Conteúdo real (texto da proposta e do folder) ainda não existe no sistema** — é o escopo da T-F3-002; hoje os 11 blocos mostram “sem texto (bloco pendente)”.
- Fonte é campo governado por seleção das três fontes aprovadas (sem alterar a coleção `dominios` da Fase 2, como previsto no plano).
- Nada das coleções das Fases 1–2 foi alterado; Cotação e campos travados do pedido intactos.

## Gate

**Teste humano do champion pendente.** Roteiro na mensagem de entrega.
