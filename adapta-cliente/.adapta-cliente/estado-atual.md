# Estado atual — Adapta Cliente

- task_id: T-F3-001 (definir conteúdo aprovado: estrutura da proposta e do folder, fontes e donos — SPEC-3-001, onda 1, única elegível)
- champion: Fábio
- spec: 04_fase-atual/specs/spec-3-001-conteudo-aprovado.md
- etapa: a_fazer
- autorizacao_implementacao: pendente (aguardando "Sim" do champion na task T-F3-001)
- teste_humano: pendente (gate do champion ao fim da task)
- verificacao_automatica: pendente (TDD CP-F3-001 na execução)
- aprendizado: n/a — transição de fase registrada no changelog v21/v22
- ultima_acao: FASE 2 FECHADA FORMALMENTE + FASE 3 LIBERADA (26/10) — revisão do consultor concluída (8/8 evidências e aceites conferidos; D1/D2 já aprovadas pelo champion 02/10); Fase 2 arquivada em 05_entregas/fase-2/ com manifesto e check; fase.md da Fase 3 com 9 tasks e 4 SPECs publicadas; STATUS 0/9; changelog v21/v22.
- proxima_acao: champion executa T-F3-001 (estrutura de blocos da proposta/folder, fontes e donos — insumo embutido na task; prazo 03/11)
- atualizado_em: 2026-10-26T18:05:00-03:00

---
## Fechamento da Fase 2 (26/10)
- Revisão do consultor (Ramon Pádua): 8/8 evidências e aceites conferidos no repositório; D1/D2 do handoff da Fase 1 já aprovadas pelo champion (02/10); check-fase-2.md assinado com active-sha256=8dde9b94f42953c2581aec9ead1852c03e7c37596062593a75b6f513f556fbdf.
- Evoluções candidatas ADIADAS: política de faturamento mínimo (pode ser criada depois; comparação segue em revisao_de_preco) e exceção de piso POR PEDIDO (entra com a política I6). Exceção da trilha segue como alteração com alçada do admin.
- Fase 2 arquivada em 05_entregas/fase-2/ (fase.md, 4 SPECs, README, manifesto adapta-phase-closure/v1 e check).

---
## Pendências herdadas da Fase 2
- Política de faturamento mínimo: SEM política cadastrada → comparação nasce em revisao_de_preco (I6).
- Exceção de piso POR PEDIDO: entra junto com a política I6; estado `excecao` já previsto no bloco Investimento; motivo do vendedor + aprovação do admin (D3).
- Exceção da trilha de preço = alteração de preço com alçada do admin (efeito: muda o preco_kg para todos). TRILHA COMO ESTÁ.

---
## Insumos do champion (T-F2-005)
- I5 componentes = formulação (custo da Cotação) + Anvisa + serviços adicionais da tabela jan-2025 (Lacre 0,60 · Montagem caixa 2,50 · Rotulagem 0,60 · Celofane 1,00), cada um com nome, valor e impacto — transparência para vendedor e cliente
- ANVISA = UMA VEZ POR PRODUTO (correção do champion 20/10): R$ 450,00 por produto na tabela jan-2025, nunca diluído por quantidade; serviços adicionais SIM por unidade
- Emenda 20/10 (autorizada): a Cotação grava mao_de_obra e anvisa (por produto) no item ao confirmar a seleção (v0.0.142, provado na tela — rascunho 0002 gravou MO '4' e anvisa '450')
- I6 ainda SEM política cadastrada → comparação nasce em revisao_de_preco
- I7 responsável por componentes e política = Fábio (admin)

---
## Decisões do champion (T-F2-002)
- I1 fonte oficial: Tabela jan-2025 (a planilha que alimenta a Cotação)
- I2 EMENDA AC-010: migrar TODAS as linhas de uma vez ("não vejo sentido em fazer isso em fases") — fim do conceito de linha piloto
- I3 responsável por produto/preço: Fábio (admin)
- I4 periodicidade: anual como padrão, mas a vigência por item (campo de validade do custo) manda — tabela jan-2025 não tem coluna de validade, então vigência padrão 31/12/2026 editável por item
- Industrialização e Serviços de Acabamento: parâmetros de custo (não migram como produtos)
- Considerações 14/10 (21:00): ID/SKU só números (máscara separadora num passo futuro); botão Editar nos itens vigentes; nomes como na tabela original; categoria Outros extinta (11 itens realocados)
- Considerações 14/10 (22:00): ID da linha de Industrialização = ID real da tabela (MO do tipo: perfume 13650, body splash 13651, aromatizador 13652) — fim do rótulo IND-

---
## Análise T-F2-001 (resumo para retomada)
- Critérios: CA-2-001 (cadastro gera produto_id único com estado), CA-2-002 (campo mínimo ausente → pendente_aprovacao com pendência nomeada, nunca visível ao vendedor), CA-2-003 (sem fonte/vigência vencida → pendência explícita, última versão aprovada mantida).
- Recorte entregue: migration 0015 criando coleção `produtos` (produto_id, nome, linha, SKU, unidade, densidade opcional, preco_kg, lote_minimo, taxas, fonte, vigência, status rascunho/pendente_aprovacao/aprovado/substituido, versao, substituido_por, responsável) com RLS (vendedor lê só aprovados; gestor/admin tudo; exclusão bloqueada) + hook de auditoria/validação + tela de cadastro/consulta + TDD CP-F2-001.
- Emenda "Linhas e Fontes" (v0.0.101–104): coleção dominios (migration 0016) + semente (0017: 10 linhas jan-2025 + 4 fontes), hook validacao_dominios, tela ListaDominios (incluir/alterar/desativar), Linha/Fonte como Select no Catálogo.
- Não alterar: Cotação (trava 16/09), campos travados do pedido (trava 26/08), coleções da Fase 1 (exceto leitura).
- Padrões reutilizados da Fase 1: RLS por papel, auditoria server-side, hooks JSVM inline, exclusão negada (retenção), estados + substituido_por.

---
## Histórico — T-F2-006 (concluída em 2026-10-23)
- v0.0.143 (coleção trilha_preco 0025 + hook fail-closed + seção no Catálogo); testes A–D OK 22/10; Parte E esclarecida (exceção da trilha = preço de tabela com alçada admin; exceção POR PEDIDO fica p/ I6); emendas de usabilidade v0.0.144–145 aprovadas; revalidação de encerramento TDD 22/22 PASS; aceite "Testei e está aprovado" 23/10; relatório 05_entregas/T-F2-006-trilha-preco-cenarios.md.

---
## Histórico — T-F2-004 (concluída em 2026-10-19)
- TDD REGRESSÃO 24/24 PASS contra o motor v0.0.137 + aceite do champion ("Funcionou — pode concluir"); relatório 05_entregas/T-F2-004-cenarios.md; achado p/ T-F2-007: UPDATE sem evento na auditoria central.

---
## Histórico — T-F1-012 (concluída em 2026-09-29)
- implementada v0.0.88–89; aprovada pelo champion; emenda toast 12s v0.0.90; D1/D2 aplicadas ao relatório em v0.0.91.

---
## Histórico — T-F1-009 (concluída em 2026-09-29)
- implementada v0.0.79–80; debugs v0.0.81–82; emendas v0.0.83–87; prova do consultor 01/10 (11/11 PASS).

---
## Análise T-F1-011 (resumo para retomada)
- Critérios: CA-1-019 e CA-1-021. Implementado: camada de validação em src/lib/baseline.ts + seção "Qualidade dos dados" no RelatorioBaseline.tsx. TDD em scripts/tf1011-tdd.py. Debug v0.0.78: chave `contraditorios` não inicializada.

---
## Histórico — T-F1-010 (concluída em 2026-09-27)
- implementada em v0.0.76; aprovado; aprendizado:AP-2026-09-27-2205.

---
## Histórico — T-F1-006 (concluída em 2026-09-27)
- implementada em v0.0.74–75; aprovado ("Funcionou"); aprendizado:AP-2026-09-27-1830.

---
## Histórico — T-F1-008 (concluída em 2026-09-25)
- implementada v0.0.63; debugs/emendas v0.0.64–v0.0.73; aprendizado:AP-2026-09-25-0940 + AP-2026-09-25-0941.

---
## Histórico — T-F1-007 (concluída em 2026-09-24)
- implementada v0.0.58; debug v0.0.60; badge v0.0.62; aprendizado:AP-2026-09-23-2015.
