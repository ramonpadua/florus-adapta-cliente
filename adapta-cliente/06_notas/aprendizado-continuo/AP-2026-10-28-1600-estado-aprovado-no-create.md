# AP-2026-10-28-1600 — Registro que nasce "aprovado" precisa da mesma rotina de substituição do UPDATE

- Status: candidato
- Escopo: projeto do cliente
- Task/SPEC: T-F3-001 · SPEC-3-001 (RN-3.003)
- Sinal: quando uma nova versão de bloco é criada já com `status = aprovado` no próprio POST
  ("Salvar e aprovar"), o caminho de CREATE não executava a rotina que marca a versão anterior
  como `substituido`. Resultado: duas versões vigentes ao mesmo tempo — exatamente o que a RN-3.003
  proíbe.
- Evidência: 1º run do TDD CP-F3-003 (`scripts/tf3001-tdd.py`), provas "CA-3-005.4 anterior marcada
  substituido" e "CA-3-005.5 exatamente 1 versão vigente" FALHARAM com `totalItems=2`; corrigido no
  hook `validacao_conteudos.js` (v0.0.166) e reexecutado: 32/32 PASS.
- Regra reutilizável: em coleção versionada, o hook de CREATE deve executar a mesma rotina de
  substituição do UPDATE sempre que o registro nascer no estado final de vigência.
- Quando aplicar: toda coleção com estados `aprovado`/`vigente` + `substituido_por` (produtos,
  trilha de preço, conteudos, e as propostas/folders das próximas tasks).
- Quando não aplicar: coleções onde a criação nunca pode nascer no estado final (ex.: rascunho
  obrigatório por regra).
- Confiança: alta — falha reproduzida e corrigida com prova de contagem (2 vigentes → 1).
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.
