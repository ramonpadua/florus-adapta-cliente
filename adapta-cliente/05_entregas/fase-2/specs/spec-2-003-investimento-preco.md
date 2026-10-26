# SPEC-2-003 — Cálculo de investimento e transparência de preço

**Fase:** 2  
**Status:** liberada  
**Dono:** vendedor; Financeiro e gestor comercial em exceções  
**Origem no escopo:** RQ-006, RQ-003, AC-001, AC-011, Fase 2 — cálculo transparente e comparação com política  
**Degrau da solução:** cálculo determinístico sobre a composição da SPEC-2-002; componentes (formulação, embalagem, frete, tributos, taxas, margem) só entram quando cadastrados com regra; política de preço/piso e alçadas são insumos do champion dentro das tasks.

## Contexto e decisões fechadas

- **Estado atual:** o vendedor digita valores parciais em documentos e explica componentes de cabeça; alteração de preço sem trilha; comparação com piso feita fora do sistema. Fontes: `01-Escopo.md` funcionalidades 4.6 e 4.8, regras propostas 3–5; vídeo `13.15.34` 02:35–03:03; perguntas 6–9 da seção 11.
- **Estado desejado:** total por linha e por projeto com componentes conhecidos separados; indicação explícita do que está incluído, excluído ou pendente; comparação com a política de qualificação/investimento mostrando a regra usada; alteração de preço com duas aprovações, histórico, vigência e rollback.
- **Decisões já fechadas:** nenhum componente desconhecido é escondido nem inferido (RQ-006); margem, tributo ou custo nunca são definidos por inferência; alteração manual de preço exige motivo, responsável e alçada; preço sem duas aprovações não fica disponível para proposta (AC-011); proposta não compara com piso de semântica indefinida (limiares R$ 30/100/150 mil seguem como parâmetro, não como regra inventada).
- **Bloqueios declarados como insumos embutidos:** política de preço/piso e semântica dos limiares, alçadas de aprovação de preço, componentes e regras de custo (formulação, embalagem, frete, tributos, taxas, margem) — champion/Financeiro entregam dentro das tasks T-F2-005/006.

## Resultado observável

O vendedor confirma uma composição calculada e o sistema exibe total por linha e por projeto com componentes separados e o que está incluído/excluído/pendente; a comparação com a política mostra a regra usada; uma alteração de preço sem as duas aprovações permanece indisponível para consumo; rollback restaura a última versão aprovada sem apagar histórico.

## Limites e dependências

- **Inclui:** cálculo de investimento sobre linhas `calculado`; componentes cadastrados com regra e fonte; comparação com política; estados `calculado`, `revisao_de_preco`, `excecao`, `aprovado`; alteração de preço com duas aprovações, trilha de versão, vigência e rollback.
- **Fora de escopo:** definir margem/tributo/custo por inferência; negociar preço automaticamente; proposta/folder (Fase 3); publicar preço para o cliente final.
- **Entradas e pré-condições:** composição da SPEC-2-002; componentes e política registrados (insumos na task); papéis e auditoria da Fase 1.
- **Saídas/artefatos:** resultado de investimento por pedido com componentes e política; trilha de versão de preço; registro de exceção.
- **Dependências e responsáveis:** SPEC-2-002; responsável por preços e Financeiro definem componentes e alçadas; gestor comercial aprova exceções.
- **Atores e permissões mínimas:** vendedor calcula e solicita exceção; gestor/admin aprovam preço (duas aprovações de pessoas distintas quando a alçada exigir); RLS e auditoria como na Fase 1.
- **Superfícies afetadas:** hooks de cálculo de investimento, tela de resultado no pedido, coleção de trilha de preço.
- **Risco e plano B:** política ausente ou fonte divergente; plano B é `revisao_de_preco` com a versão anterior aprovada preservada e pendência aberta — o fluxo comercial manual segue.
- **Rollback ou reversão:** restaurar a última versão aprovada do preço com trilha; nenhuma exclusão; cálculo desativável sem perder dados.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Composição → investimento | Linhas `calculado` (SPEC-2-002) | Produto, quantidade convertida, componentes, fórmula | Hook server-side | Recálculo idempotente por pedido/versão | Linha não calculável não entra no total |
| Componentes → cálculo | Cadastro com regra e fonte | Componente, valor/regra, vigência, responsável | Gestor/admin | Idempotência por componente+versão | Componente ausente → "pendente" visível, nunca inferido |
| Política → comparação | Política cadastrada (insumo champion) | Limiar, semântica, ação | Leitura server-side | Leitura pura | Sem política → comparação fica `revisao_de_preco` |
| Trilha de preço | Coleção de versões | Preço, versão, aprovadores (2), motivo, vigência, `rollback_de` | Gestor/admin; auditoria append-only | Escrita única; rollback = nova versão | Sem duas aprovações → indisponível para consumo |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-2.011 | Composição confirmada | Total por linha e por projeto com componentes separados e incluído/excluído/pendente explícitos | Componente não cadastrado → marcado pendente, nunca estimado | RQ-006 |
| RN-2.012 | Política cadastrada | Comparação exibindo a regra usada (limiar e semântica) | Sem política → `revisao_de_preco`, sem número publicado | RQ-003, RQ-006 |
| RN-2.013 | Alteração de preço | Exige duas aprovações de pessoas com alçada; sem elas, indisponível | Emergência registrada como exceção auditada | AC-011; RQ-006 |
| RN-2.014 | Fonte desatualizada ou divergente | `revisao_de_preco`; versão anterior aprovada preservada | Reconciliação resolve | RQ-006 |
| RN-2.015 | Rollback de preço | Nova versão restaurando a última aprovada; histórico íntegro | — | RQ-011; padrão T-F1-009 |
| RN-2.016 | Exceção de preço/piso | Motivo, responsável e aprovação com alçada registrados | Sem alçada → negado com motivo do servidor | RQ-006 |

## Fluxo e regras

1. Vendedor confirma a composição; sistema recupera valores vigentes dos componentes.
2. Calcula total por linha e por projeto; separa componentes; marca incluído/excluído/pendente.
3. Compara com a política cadastrada exibindo a regra usada.
4. Exceção registra motivo, responsável e aprovação com alçada.
5. Alteração de preço abre trilha: rascunho → aprovação 1 → aprovação 2 → vigente; rollback = nova versão.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Composição calculada com componentes e política | Totais com componentes e comparação com a regra visível | Componente ausente → pendente explícito |
| Limite | Política não cadastrada ou fonte divergente | `revisao_de_preco` sem número publicado; versão anterior válida | Champion cadastra → recálculo |
| Falha | Preço com uma aprovação só ou rollback | Indisponível para consumo; rollback restaura versão aprovada com trilha | Auditoria registra negativa e motivo |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; RQ-006 e RQ-003; padrões de trilha/rollback da SPEC-1-003 (T-F1-009).
2. **Alterar somente:** cálculo de investimento, tela de resultado, trilha de preço, exceções.
3. **Não alterar:** catálogo e composição (leitura), Cotação existente, proposta (Fase 3), limiares como regra fixa.
4. **Executar nesta ordem:** registrar componentes/política/alçadas (insumo) → cálculo → comparação → trilha de preço com duas aprovações → rollback → demonstração.
5. **Parar e pedir validação quando:** política/alçadas não chegarem com o champion na task (embutir pergunta, não travar); cálculo exigir custo não cadastrado.
6. **Estado válido ao parar:** versões aprovadas preservadas; cálculo desativável; operação manual intacta.

## Checklist de execução

- [ ] Componentes e política de preço registrados pelo champion/Financeiro.
- [ ] Total por linha e por projeto com incluído/excluído/pendente demonstrados.
- [ ] Comparação com a política exibindo a regra usada.
- [ ] Alteração de preço com duas aprovações, vigência e trilha.
- [ ] Preço sem duas aprovações indisponível para consumo (prova negativa).
- [ ] Rollback restaurando a última versão aprovada sem apagar histórico.

## Critérios de aceite

- [ ] **CA-2-013:** composição confirmada gera total por linha e por projeto com componentes separados e incluído/excluído/pendente explícitos.
- [ ] **CA-2-014:** comparação com a política mostra a regra usada; sem política, `revisao_de_preco` sem número publicado.
- [ ] **CA-2-015:** alteração de preço sem duas aprovações permanece indisponível para consumo.
- [ ] **CA-2-016:** exceção de preço/piso registra motivo, responsável e aprovação com alçada; negativa auditada com motivo do servidor.
- [ ] **CA-2-017:** fonte desatualizada/divergente gera `revisao_de_preco` preservando a versão anterior aprovada.
- [ ] **CA-2-018:** rollback de preço restaura a última versão aprovada com trilha íntegra.

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Cotar investimento hoje | Pedir ao vendedor para montar o total de um pedido com componentes | Valores parciais digitados manualmente, sem trilha — registrar a lacuna | Roteiro/captura |
| GREEN | Calcular com CP-F2-007 | Composição calculada + componentes + política | Totais com componentes e comparação com a regra | Exportação + capturas |
| REFACTOR/REGRESSÃO | Repetir com CP-F2-008/009 | Preço com 1 aprovação, rollback, fonte divergente | Indisponível; restauração com trilha; `revisao_de_preco` | Relatório de cenários + trilha |

**Dados/fixtures:** CP-F2-007: pedido com composição calculada, componentes e política cadastrados; CP-F2-008: preço com apenas uma aprovação; CP-F2-009: preço divergente entre versões.

**Caminhos de erro obrigatórios:** componente ausente, política ausente, uma aprovação só, negativa sem alçada, rollback, sessão expirada.

**Evidência exigida:** exportação do cálculo com componentes, trilha de preço com aprovações, capturas da comparação e aceite humano do gestor/Financeiro.

## Handoff e operação

- **Como demonstrar:** calcular o pedido piloto ao vivo, mostrar componentes e a regra da política, tentar aprovar preço sozinho (negativa auditada) e executar o rollback.
- **Como operar depois:** vendedor calcula; gestor/Financeiro aprovam preços e exceções; pendências de política visíveis ao responsável.
- **Como monitorar:** percentual de cálculos com componentes completos; exceções de preço; rollbacks; tempo de cotação.
- **Pendência conhecida:** política/alçadas/componentes são insumos do champion/Financeiro nas tasks; sem eles, `revisao_de_preco` sem travar o fluxo.

## Tasks vinculadas

| ID | Onda | Task | Critério coberto | Recorte da prova | Predecessoras | Status |
|---|---|---|---|---|---|---|
| `T-F2-005` | 5 | Configurar cálculo de investimento, componentes e comparação com política | CA-2-013, CA-2-014, CA-2-017 | TDD GREEN com CP-F2-007 | `T-F2-003` | Pendente |
| `T-F2-006` | 6 | Exercitar duas aprovações, exceções e rollback de preço | CA-2-015, CA-2-016, CA-2-018 | TDD REGRESSÃO com CP-F2-008/009 | `T-F2-005` | Pendente |

## Emendas

Nenhuma emenda registrada.
