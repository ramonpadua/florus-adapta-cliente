# SPEC-4-004 — Idempotência de envio, demonstração do ciclo e fechamento

**Fase:** 4
**Status:** liberada
**Dono:** vendedor (envia e registra); gestor/admin (auditoria e exportação); champion Fábio (caso real da demonstração); consultor (decisão da chave de idempotência — pendência P3 da Fase 3)
**Origem no escopo:** Fase 4 — fecho das regras de recuperação e duplicidade da fase + demonstração de aceite binário; pendência P3 registrada na T-F3-008 (`05_entregas/T-F3-008-demonstracao-fluxo-completo.md`, seção 7)
**Degrau da solução:** correção da idempotência de envio dentro das coleções existentes (`envios`, Fase 3) + demonstração ponta a ponta do ciclo pós-envio com caso real; nenhuma integração externa presumida.

## Contexto e decisões fechadas

- **Estado atual:** a idempotência de envio compara `documento + versão + destinatário`, e destinatário é TEXTO LIVRE — "Maria Erotides" e "Maria — Gerente de Compras (+55 11 99311-7171)" passam como destinatários distintos e geram envio duplicado (achado real da T-F3-008). Existe a ação `marcar_duplicado` (marca sem apagar, motivo ≥ 10, fail-closed, auditada; ENV-2026-0011 marcado, ENV-2026-0012 válido), mas a regra em si não foi corrigida.
- **Estado desejado:** chave ESTÁVEL de destinatário no registro de envio — a mesma pessoa escrita de dois jeitos gera UM registro; e a demonstração do ciclo pós-envio inteiro num caso real: envio → próxima ação → lembrete → aceite de amostra → cobrança observada → handoff → retorno/escalonamento → trilha exportável.
- **Decisões já fechadas:** a correção da chave é decisão do consultor com o champion (pendência P3 da Fase 3); as duas saídas em avaliação são (A) vincular o envio ao contato do pedido (pessoa já cadastrada) ou (B) comparar por telefone/e-mail; qualquer API, custo ou credencial nova é gate do consultor; envio manual registrado permanece (decisão T-F3-006); nada é disparado para cliente real sem confirmação na hora (regra do ensaio, T-F3-008).
- **Insumos embutidos (não travam o início):** qual caso real serve de demonstração e quem acompanha — champion entrega na task T-F4-008; a escolha da chave (A ou B) é confirmada na task T-F4-007.

## Resultado observável

O mesmo envio para a mesma pessoa escrita de dois jeitos gera um registro só, com a negativa visível na segunda tentativa; o ciclo pós-envio inteiro é demonstrado num caso real com roteiro humano e a trilha do ciclo sai em planilha com as pendências nomeadas com dono.

## Limites e dependências

- **Inclui:** chave estável de destinatário em `envios`; correção compatível com os registros existentes (marcados como duplicado permanecem marcados); demonstração do ciclo com caso real; exportação da trilha.
- **Fora de escopo:** reenvio automático (Fase 4 não envia sozinha); alterar o fato do envio já registrado (ato consumado é histórico — padrão T-F3-006); integração com CRM externo (dependência condicional).
- **Entradas e pré-condições:** T-F4-001 a T-F4-006 concluídas para a demonstração completa; envios e `marcar_duplicado` da Fase 3 intactos.
- **Saídas/artefatos:** hook corrigido; relatório da demonstração; CSV da trilha do ciclo.
- **Dependências e responsáveis:** consultor + champion escolhem a chave; champion escolhe o caso real.
- **Atores e permissões mínimas:** vendedor envia e registra nas próprias; gestor/admin audita e exporta; sem alçada definida → negado (fail-closed).
- **Superfícies afetadas:** hook `registro_envios.js`; telas de envio e trilha (leitura).
- **Risco e plano B:** chave (A) exigir contato cadastrado que nem todo envio tem → fallback para (B) comparando telefone/e-mail normalizados.
- **Rollback:** envio é ato consumado; correção vale para novos registros; duplicados históricos permanecem marcados com `marcar_duplicado`.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Envio → chave de destinatário | Contato do pedido (A) ou telefone/e-mail (B) | Chave estável por pessoa | RLS Fase 3 | Mesma chave + documento + versão → 1 registro | Segunda tentativa negada com motivo |
| Envio → trilha | Auditoria server-side | `pedido_id_ref`, ator, motivo | Server-side hooks | Append-only | Nada é apagado |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-4.016 | Mesma pessoa, texto diferente | 1 registro só; negativa visível na segunda tentativa | — | Pendência P3 / T-F3-008 |
| RN-4.017 | Envio histórico marcado como duplicado | Permanece marcado; não é reescrito | — | Decisão T-F3-007 (auditoria corrigida deixa de ser auditoria) |
| RN-4.018 | Ensaio da demonstração | Nada disparado para cliente real sem ok na hora | Sem ok → para antes do envio | Regra T-F3-008 |

## Fluxo e regras

1. Consultor + champion confirmam a chave estável (A ou B) na task T-F4-007.
2. Hook `registro_envios.js` passa a comparar pela chave; registros existentes não são reescritos.
3. Prova negativa: mesmo destinatário com texto diferente → negativa com motivo.
4. Demonstração do ciclo inteiro com caso real escolhido pelo champion (T-F4-008), uma etapa por vez com aceite.
5. Trilha do ciclo exportada em CSV sanitizado; pendências nomeadas com dono.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Envio para pessoa cadastrada | 1 registro com chave estável | — |
| Limite | Mesma pessoa, texto digitado diferente | Segunda tentativa negada com motivo | `marcar_duplicado` para legados |
| Falha | Ensaio sem ok do champion | Para antes de qualquer envio | Retomada com confirmação |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; `05_entregas/T-F3-008-demonstracao-fluxo-completo.md` (seções 4.3 e 7 — a pendência P3 e as duas saídas em avaliação).
2. **Alterar somente:** hook `registro_envios.js`, telas de envio/trilha (leitura), relatório da demonstração.
3. **Não alterar:** registros de envio existentes (exceto pela chave nos novos), coleções das Fases 1–3 (exceto leitura), Cotação, campos travados.
4. **Executar nesta ordem:** confirmar a chave (A ou B) → corrigir o hook → prova negativa → demonstração do ciclo → exportação da trilha.
5. **Parar e pedir validação quando:** o champion não escolher o caso real; qualquer API, custo ou credencial nova (gate do consultor).
6. **Estado válido ao parar:** envios históricos intactos, duplicados marcados, pendências nomeadas.

## Checklist de execução

- [ ] Chave estável de destinatário confirmada (A ou B) pelo consultor com o champion.
- [ ] Hook corrigido; segunda tentativa com texto diferente negada (prova negativa).
- [ ] Registros históricos marcados como duplicado permanecem marcados.
- [ ] Demonstração do ciclo completo com caso real, uma etapa por vez com aceite.
- [ ] Trilha do ciclo exportada em CSV sanitizado; pendências nomeadas com dono.

## Critérios de aceite

- [ ] **CA-4-016:** mesmo destinatário escrito de dois jeitos gera um registro só, com negativa visível na segunda tentativa.
- [ ] **CA-4-017:** registros históricos marcados como duplicado permanecem marcados (nada é reescrito).
- [ ] **CA-4-018:** demonstração do ciclo pós-envio inteiro com caso real, com roteiro humano aceito etapa por etapa.
- [ ] **CA-4-019:** trilha do ciclo exportável em CSV sanitizado com as pendências nomeadas com dono.
- [ ] **CA-4-020:** nenhuma ação do ciclo é disparada para cliente real sem confirmação na hora (regra do ensaio).

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Idempotência hoje | Enviar para a mesma pessoa com texto diferente | 2 registros (defeito P3) | Relatório T-F3-008 |
| GREEN | CP-F4-016/017 | Enviar com chave estável; conferir legado | 1 registro; legado marcado intacto | Relatório de cenários |
| REGRESSÃO | CP-F4-018/019/020 | Ciclo completo no caso real; exportar | Etapas aceitas; CSV com pendências | Relatório da demonstração |

## Tasks vinculadas

- T-F4-007 (onda 7)
- T-F4-008 (onda 8)
