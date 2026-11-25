# T-F4-001 — Próxima ação depois do envio e fila de oportunidades

**Fase:** 4 · **SPEC:** SPEC-4-001 (onda 1) · **Versão:** v0.0.227 · **Data:** 26/11/2026
**Champion:** Fábio · **Aceite:** "Deu tudo certo" (26/11/2026, roteiro de 5 passos)

---

## 1. O que a task entrega

O envio da proposta deixa de ser um ponto final. No momento em que o envio é registrado, a
proposta ganha uma **próxima ação** — o quê, quem e até quando — e entra numa **fila de
oportunidades** que o gestor abre e vê quem está parado e quem passou do prazo.

**Regra da fase, cumprida sem exceção:** o follow-up **não envia mensagem, não negocia, não
desqualifica e não fecha sozinho**. Ele organiza e registra; quem age é o vendedor, no WhatsApp,
e o resultado volta para o sistema.

## 2. Insumos decididos pelo champion (não são números meus)

| Insumo | Decisão | Onde foi aplicado |
|---|---|---|
| SLA padrão | **3 dias** contados da data do envio, configurável, trocável depois | semente em `config_followup` (`sla_dias=3`), gravado em cada follow-up no campo `sla_dias` |
| Troca do SLA | Permitida depois, com registro na Trilha (de→para, autor, data) | hook `followup_config_sla.js` + alçada `configurar_sla` (gestor/admin) |
| Visibilidade da fila | **Gestor e admin veem tudo; vendedor vê só as dele** | regra do servidor em `/fila-oportunidades` + RLS da coleção `followups` |

O SLA foi perguntado e respondido em duas etapas: o champion quis saber primeiro se poderia
mudar depois (sim, e é assim que foi construído), e só então escolheu o número.

## 3. O que foi construído

### Banco (migration 0045 — conferida como `applied` no schema)

- **`followups`** — a próxima ação: `followup_id` (FU-ano-####, gerado no servidor), vínculo com
  `envio_id`/`pedido_id`/`documento_id`, `proxima_acao`, `vendedor_id`/`vendedor_nome`,
  `sla_dias`, `enviado_em`, `prazo_em`, `estado` (`aberta|concluida|encerrada`), `origem`
  (`envio|migracao|manual`), `motivo`. Índice **único em `envio_id`** = um envio, uma próxima
  ação. Retenção: `deleteRule: null`.
- **`config_followup`** — parâmetro governado do SLA (`chave`, `sla_dias`, autor, data, motivo,
  fonte). Leitura para autenticados; escrita só gestor/admin; sem criação por API.
- **Backfill** — o envio real **ENV-2026-0012** (Maria Erotides SA) nasceu na Fase 3 sem próxima
  ação; a migration criou **FU-2026-0001** com prazo **28/11/2026**.
- **Alçadas novas (fail-closed):** `configurar_sla` (gestor) e `criar_followup_manual` (gestor).

### Servidor

- **`registro_envios.js`** — depois de o envio ser gravado, cria a próxima ação daquele envio
  (idempotente). Também registra `criar_followup` na Trilha com `pedido_id_ref`.
- **`followup_config_sla.js`** — troca de SLA: alçada, motivo ≥ 10, de→para na Trilha.
- **`followup_acoes.js`** — recuperação manual de envio sem próxima ação: gestor/admin, motivo
  ≥ 10, dados vindos do envio, duplicata negada com o número da existente.
- **`fila_oportunidades.js`** — rota `GET /fila-oportunidades`: idade, prazo, atraso e
  visibilidade calculados **no servidor**; pedido de ensaio (PROVA/DEMO/CP-F/LIMPEZA ou tag
  `limpeza-prova`) fica fora da tela do champion.

### Tela

- **`FilaOportunidades.tsx`**, botão **"Fila"** no menu do topo (entre Envios e Trilha):
  três quadros (aguardando · passaram do prazo · prazo padrão), bloco próprio **"Ações da fila"**
  com legenda de uma linha por botão, lista com cartão por oportunidade (cliente, FU, próxima
  ação, proposta/versão, envio, canal, destinatário, responsável, prazo) e destaque âmbar no
  atraso. Bloco de **envios sem próxima ação** com dono nomeado.
- **`TrilhaAuditoria.tsx`** — as três ações novas com rótulo em português.

## 4. Prova por critério da SPEC

Revalidação do zero em 26/11/2026 — **21/21 PASS** (`scripts/tf4001-revalidacao.py`):

| Critério | Prova | Resultado |
|---|---|---|
| **CA-4-001** | envio real ENV-2026-0012 tem exatamente 1 próxima ação (FU-2026-0001); a fila mostra a oportunidade com idade, prazo e próxima ação | PASSOU |
| **CA-4-001** | prazo = 25/11 (envio) + 3 dias = **28/11/2026**; `sla_dias=3` gravado no registro; estado `aberta` | PASSOU |
| **CA-4-001** | fila não expõe pedido de ensaio (n=1, só a Maria) | PASSOU |
| **CA-4-002** | dentro do prazo não é marcada como atrasada (`atrasada=false`, faltam 2 dias) | PASSOU |
| **CA-4-002** | contador de atrasadas coerente com a lista (0 = 0) | PASSOU |
| **CA-4-002** | cálculo pela data do ENVIO: registro com envio antigo ficou com prazo vencido (18/11) | PASSOU |
| **CA-4-002** | filtro `atrasadas=1` nunca devolve item não atrasado | PASSOU |
| **CA-4-003** | adiar/encerrar não existem nesta onda — ação de fechar é negada (403) | PASSOU (onda 2) |
| **CA-4-004** | reabertura pertence à T-F4-002 | — (onda 2) |
| **CA-4-005** | vendedor não altera o SLA e o valor segue intacto (negado, sla=3) | PASSOU |
| **CA-4-005** | motivo curto é negado e o SLA não muda (400) | PASSOU |
| **CA-4-005** | vendedor não cria próxima ação manualmente (400) | PASSOU |
| **CA-4-005** | vendedor vê só as dele; gestor vê tudo | PASSOU |
| **RN-4.001** | as quatro ações proibidas (enviar mensagem, negociar, desqualificar, fechar) são negadas | PASSOU |

Prova funcional da criação (TDD, 18/18 PASS, `scripts/tf4001-tdd.py`): envio novo cria a próxima
ação com prazo pela data do envio; reenvio idêntico é negado e não duplica; troca de SLA com
motivo grava de→para (3→5) na Trilha.

## 5. Defeitos reais encontrados e corrigidos durante a execução

Todos foram achados **executando**, não lendo o código:

1. **A migration não aplicou** apesar do QA dizer `ok` (lição conhecida desde 18/11). Recarga do
   arquivo + novo apply resolveu; conferido no **schema** (`followups` e `config_followup` existem).
2. **Rota no caminho errado** — registrei em `/backend/v1/fila-oportunidades` e este projeto expõe
   rotas na raiz (`/fila-pendencias`, `/exportar-trilha`). Passou a `/fila-oportunidades`.
3. **Dois hooks no mesmo arquivo** — só o primeiro carrega; a troca de SLA passava sem alçada e
   sem trilha. Separado em `followup_config_sla.js` e `followup_acoes.js`.
4. **Hook `onRecordAfterCreateSuccess` não dispara nesta instância** — o envio era gravado e
   nenhuma próxima ação nascia. A criação foi movida para dentro do hook do envio, que é o único
   ponto de execução comprovado (documentado no próprio arquivo).
5. **Fila mostrava cliente de ensaio** ("PROVA T-F3-008 (ensaio)") — filtro passou a olhar também
   o nome do cliente, não só o número do pedido.
6. **Erro meu de prova, não do sistema:** dois testes meus usavam motivo com mais de 10 caracteres
   e concluíam "motivo curto aceito"; e uma prova minha deixou o SLA em 9 dias. Corrigi o teste e
   **restaurei o SLA para 3 dias** (decisão do champion), conferido no registro e na fila.

## 6. Estado deixado no sistema

- Fila: **1 oportunidade** (Maria Erotides SA, FU-2026-0001, prazo 28/11/2026, **0 atrasadas**).
- SLA vigente: **3 dias**.
- Envios, propostas e folders da Fase 3: **intactos** (esta task só leu).
- Resíduo de prova: **3 registros de ensaio** — FU-2026-0002 (ENV-2026-0016) e FU-2026-0003
  (ENV-2026-0017), de clientes com nome "PROVA"/"ENSAIO". Eles **não aparecem na fila** (filtro de
  ensaio) e o envio não pode ser apagado (retenção da Fase 3). Ficam registrados aqui para não
  parecerem oportunidade real.
- Trilha: dois eventos de troca de prazo do meu teste (3→5 e 5→3) e um (9→3) de restauração.
  Auditoria não se reescreve — ficam visíveis e explicados aqui.

## 7. O que está fora desta task (e fica para a onda 2)

Lembrete ao dono, **adiar com motivo**, **encerrar com motivo** e **reabrir com histórico** são a
**T-F4-002**, como o contrato da fase define. Nesta onda, a coleção está **fechada para edição por
API** (`updateRule: null`) — é o fail-closed: nenhuma transição acontece antes de existir a regra
dela.

## 8. Aprendizado

- **"QA ok" não prova que a migration aplicou.** Conferir o schema é parte da prova, sempre.
- **Um hook por arquivo**, e nem todo tipo de hook dispara nesta instância: o ponto de execução
  precisa ser **provado**, não suposto. Foi o `onRecordAfterCreateSuccess` que quebrou o desenho
  mais bem intencionado desta task.
- **O teste também erra.** Duas falhas da revalidação eram do meu script (motivo de 27 caracteres
  chamado de "curto", e SLA deixado em 9 por falta de restauração). Antes de acusar o sistema,
  reler a própria prova — e quando a prova muda estado, **restaurar sempre**.
- **Fila de governança precisa excluir ensaio pelo dado, não só pelo número do pedido**: o nome do
  cliente também identifica prova.

## 9. Arquivos

**Produto (Skip):** `pocketbase/migrations/0045_create_followups.js` ·
`pocketbase/hooks/followup_proxima_acao.js` · `pocketbase/hooks/followup_config_sla.js` ·
`pocketbase/hooks/followup_acoes.js` · `pocketbase/hooks/fila_oportunidades.js` ·
`pocketbase/hooks/registro_envios.js` (criação da próxima ação) ·
`src/components/FilaOportunidades.tsx` · `src/components/Layout.tsx` · `src/App.tsx` ·
`src/components/TrilhaAuditoria.tsx` · `package.json` (v0.0.227)

**Provas:** `scripts/tf4001-tdd.py` (18/18) · `scripts/tf4001-revalidacao.py` (21/21)
