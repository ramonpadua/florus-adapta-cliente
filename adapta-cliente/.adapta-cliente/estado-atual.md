# Estado atual — Adapta Cliente

- task_id: T-F3-002 (migrar e aprovar o conteúdo piloto, com pendências nomeadas — SPEC-3-001, onda 2)
- champion: Fábio
- spec: 04_fase-atual/specs/spec-3-001-conteudo-aprovado.md
- etapa: aguardando_teste_humano
- autorizacao_implementacao: confirmada (2026-10-29, mensagem: "Sim, mas tenha atenção nos alertas para o cliente (células destacadas em verde) e nas etapas da entrega do projeto. Note também que o cliente deve retornar a proposta assinada, com destaque para as amostras que ele aprovou.")
- teste_humano: pendente
- verificacao_automatica: passou (TDD CP-F3-002/003 22/22 PASS na v0.0.175; QA Skip completo OK em v0.0.172, 173, 174 e 175)
- aprendizado: pendente
- ultima_acao: T-F3-002 implementada (29/10) — 8 blocos + campos de conferência; 5 blocos migrados do PDF Proposta 2026-0212 com ZERO divergências; alertas verdes e as 20 etapas preservados; amostras + assinatura no bloco de próximos passos; 2 achados reais corrigidos (JSONField []byte)
- proxima_acao: apresentar o roteiro de teste humano ao champion e aguardar o aceite
- atualizado_em: 2026-10-29T17:05:00-03:00

---
## Fechamento da Fase 2 (26/10)
- Revisão do consultor (Ramon Pádua): 8/8 evidências e aceites conferidos no repositório; D1/D2 do handoff da Fase 1 já aprovadas pelo champion (02/10); check-fase-2.md assinado com active-sha256=8dde9b94f42953c2581aec9ead1852c03e7c37596062593a75b6f513f556fbdf.
- Evoluções candidatas ADIADAS: política de faturamento mínimo (pode ser criada depois; comparação segue em revisao_de_preco) e exceção de piso POR PEDIDO (entra com a política I6). Exceção da trilha segue como alteração com alçada do admin.
- Fase 2 arquivada em 05_entregas/fase-2/ (fase.md, 4 SPECs, README, manifesto adapta-phase-closure/v1 e check).

---
## Decisões do champion — T-F3-002 (29/10)
- A proposta atual (PDF Proposta Comercial 2026-0212, cliente Lotcare) é **ponto de partida**, não modelo final: está obsoleta e com visual ruim, será reformulada.
- Conteúdo novo desejado: **propósito do projeto** · **por que da linha oferecida** · **matérias-primas de cada produto e o que elas fazem** · **procedimentos que podem ser feitos com os produtos**.
- Estrutura da proposta passa de 6 para **8 blocos** (+ `materias_primas`, + `procedimentos`).
- Atenção às **células destacadas em verde** (títulos de alerta ao cliente) e às **etapas da entrega do projeto**.
- O cliente deve **retornar a proposta assinada, com destaque para as amostras que aprovou**.
- Texto de teste PROP-0019 substituído pela Capa real (virou histórico `substituido`).

---
## Decisões do champion — T-F3-001 (28/10, formulário de autorização)
- Estrutura aprovada: **6 blocos de proposta** (capa · entendimento · escopo · investimento · condições · próximos passos) e **5 blocos de folder** (capa/identidade · linhas · produtos em destaque · alegações · contato).
- **Gestor/admin aprova todos os blocos** (mesma regra do catálogo da Fase 2); dono do texto registrado no bloco.
- **Três fontes declaradas por bloco:** documentos comerciais vigentes da Florus · catálogo governado (produtos aprovados) · Regulatório/ANVISA (alegações).
- Vigência padrão **31/12/2026**, editável por bloco.
- Expansão para novos blocos só entra após novo aceite do champion (SPEC-3-001, item 6).

---
## Pendências herdadas da Fase 2
- Política de faturamento mínimo: SEM política cadastrada → comparação nasce em revisao_de_preco (I6).
- Exceção de piso POR PEDIDO: entra junto com a política I6; estado `excecao` já previsto no bloco Investimento; motivo do vendedor + aprovação do admin (D3).
- Exceção da trilha de preço = alteração de preço com alçada do admin (efeito: muda o preco_kg para todos). TRILHA COMO ESTÁ.

---
## Insumos do champion (T-F2-005)
- I5 componentes = formulação (custo da Cotação) + Anvisa + serviços adicionais da tabela jan-2025 (Lacre 0,60 · Montagem caixa 2,50 · Rotulagem 0,60 · Celofane 1,00), cada um com nome, valor e impacto
- ANVISA = UMA VEZ POR PRODUTO (correção do champion 20/10): R$ 450,00 por produto na tabela jan-2025, nunca diluído por quantidade; serviços adicionais SIM por unidade
- Emenda 20/10 (autorizada): a Cotação grava mao_de_obra e anvisa (por produto) no item ao confirmar a seleção (v0.0.142, provado na tela)
- I6 ainda SEM política cadastrada → comparação nasce em revisao_de_preco
- I7 responsável por componentes e política = Fábio (admin)

---
## Decisões do champion (T-F2-002)
- I1 fonte oficial: Tabela jan-2025 (a planilha que alimenta a Cotação)
- I2 EMENDA AC-010: migrar TODAS as linhas de uma vez — fim do conceito de linha piloto
- I3 responsável por produto/preço: Fábio (admin)
- I4 vigência anual como padrão, mas a vigência por item manda — vigência padrão 31/12/2026 editável por item
- Industrialização e Serviços de Acabamento: parâmetros de custo (não migram como produtos)
- Considerações 14/10: ID/SKU só números; botão Editar nos itens vigentes; nomes como na tabela original; categoria Outros extinta
- ID da linha de Industrialização = ID real da tabela (13650/13651/13652)

---
## Análise T-F2-001 (resumo para retomada)
- Critérios: CA-2-001, CA-2-002 e CA-2-003 (produto_id único com estado, pendência nomeada nunca visível ao vendedor, fonte/vigência explícitas).
- Recorte entregue: migration 0015 `produtos` (RLS vendedor só aprovados; gestor/admin tudo; exclusão bloqueada) + hook de auditoria/validação + tela de cadastro/consulta + TDD CP-F2-001.
- Emenda "Linhas e Fontes" (v0.0.101–104): coleção dominios (0016) + semente (0017), hook validacao_dominios, tela ListaDominios.
- Não alterar: Cotação (trava 16/09), campos travados do pedido (trava 26/08), coleções da Fase 1 (exceto leitura).
- Padrões reutilizados: RLS por papel, auditoria server-side, hooks JSVM inline, exclusão negada (retenção), estados + substituido_por.

---
## Histórico — T-F2-006 (concluída em 2026-10-23)
- v0.0.143 (trilha_preco 0025 + hook fail-closed + seção no Catálogo); emendas de usabilidade v0.0.144–145; revalidação 22/22 PASS; aceite "Testei e está aprovado"; relatório 05_entregas/T-F2-006-trilha-preco-cenarios.md.

---
## Histórico — T-F2-004 (concluída em 2026-10-19)
- TDD REGRESSÃO 24/24 PASS + aceite do champion; relatório 05_entregas/T-F2-004-cenarios.md.

---
## Histórico — T-F1-012 / T-F1-009 / T-F1-011 / T-F1-010 / T-F1-008 / T-F1-007
- T-F1-012 (29/09, v0.0.88–91 com emenda toast 12s) · T-F1-009 (29/09, v0.0.79–87; prova do consultor 11/11) · T-F1-011 (baseline/qualidade de dados; debug v0.0.78) · T-F1-010 (27/09, v0.0.76) · T-F1-008 (25/09, v0.0.63–73) · T-F1-007 (24/09, v0.0.58–62).
