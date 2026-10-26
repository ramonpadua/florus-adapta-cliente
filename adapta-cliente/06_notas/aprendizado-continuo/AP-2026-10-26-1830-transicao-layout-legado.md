# AP-2026-10-26-1830 — Transição de fase em repo com layout legado

**Contexto:** fechamento da Fase 2 e liberação da Fase 3 do Florus (26/10), executados pelo
consultor responsável com apoio do agente.

**Aprendizado reutilizável:** os scripts atuais do plugin Consultor Adapta
(`skill-mind-run.mjs`, `preparar-liberacao-fase.mjs`) resolvem a raiz do plano pelo layout novo
(`03-Projeto/` ou filho `Plano — <id>`). Repos de clientes no layout legado
(`adapta-cliente/` com `01_projeto/`, `04_fase-atual/`, `05_entregas/`) não são resolvidos —
o run do SkillMind não abre por script.

**Procedimento validado nesta transição (fallback serial, gates preservados):**

1. Registrar a limitação técnica e executar o ritual inline, em série, no agente principal.
2. Espelhar a convenção de fechamento já usada pelo próprio repo (Fase 1):
   arquivar `04_fase-atual/` em `05_entregas/fase-N/` (fase.md + specs + README) +
   `phase-closure-manifest.json` (schema `adapta-phase-closure/v1`, hash sha256 por arquivo,
   `activeDigest` do snapshot de `04_fase-atual/`, `checkSha256`) + `check-fase-N.md` assinado
   com `active-sha256=<digest>`.
3. Calcular o digest do snapshot de `04_fase-atual/` com o mesmo algoritmo do script
   (sha256 do JSON do snapshot ordenado por caminho relativo).
4. Publicar a fase N+1 em `04_fase-atual/` (fase.md + specs + índice), remover as SPECs da
   fase encerrada da pasta ativa, atualizar STATUS.md, changelog.md e estado-atual.md.
5. Validar tudo por script antes do commit (contagem de tasks/ids, seções das SPECs, hashes
   do manifesto, digest no check, coerência STATUS/changelog).
6. Commit local selado; push só após confirmação explícita do consultor.

**Gatilho de reuso:** toda transição de fase em repo com layout legado (Florus e clientes
antigos). Se o repo migrar para o layout novo, voltar aos scripts nativos.
