# T-F3-008 — Demonstração do fluxo completo com caso real

**Fase:** 3 · **Task:** T-F3-008 · **Champion:** Fábio (Florus Brasil)
**Concluída em:** 25/11/2026 · **Versões:** v0.0.216 → v0.0.219 · **Migrations:** 0043, 0044

---

## 1. O que a task pedia

Demonstrar o processo comercial de ponta a ponta num **caso real** — o pedido entra, a proposta é montada, o vendedor revisa, o envio fica registrado, o folder sai com os blocos aprovados — e fechar com a lista de pendências de conteúdo (com dono e motivo) e a exportação da trilha em planilha.

**Regra do ensaio, definida pelo champion:** nada é disparado para cliente real sem a confirmação dele na hora; sem ok, o ensaio para antes do envio.

## 2. Decisões do champion que definiram o ensaio

| Data | Decisão | Consequência |
|---|---|---|
| 25/11 | **Caso real:** Maria Erotides SA — pedido **20260929-0003** ("Linha para careca") | Todo o ensaio roda no pedido real, não em dado de prova |
| 25/11 | **Acompanhamento:** somente o próprio Fábio | Roteiro no chat, uma etapa por vez, com aceite antes de avançar |
| 25/11 | **Ordem do processo comercial** (correção dele) | A proposta serve para o cliente ver o **valor**; amostras vêm **depois** da aprovação dos valores e o aceite vem depois do teste |
| 25/11 | Corrigir a ordem "agora" | v0.0.216 + migration 0043 |
| 25/11 | Proposta revisada e envio registrado | envio **ENV-2026-0012** |
| 25/11 | O registro correto é o novo; o anterior é duplicado | v0.0.218, ação `marcar_duplicado` |
| 25/11 | **Corrigir a trilha também nos eventos de hoje** | v0.0.219 + migration 0044 |
| 25/11 | **Aceite final:** "Aprovada, com as pendências registradas" | Fechamento com ressalvas nominadas (seção 7) |

## 3. As sete etapas, com o resultado observado

| # | Etapa | Resultado real | Aceite |
|---|---|---|---|
| 1 | Pedido entrando | 20260929-0003, Maria Erotides SA (MARE), versão 9, 3 itens de 100 mL/g × 1.000 un, referência Kensho | Aprovado |
| 2 | Vinculação do produto | 3 linhas vinculadas e **aprovadas por Fabio Sacheto** (13067, 13138, 13064); sugeridas por Fabiana Kaori | Aprovado |
| 3 | Proposta montada | **2026-0077**: R$ 9,94 / 10,38 / 20,83 por unidade; produção R$ 41.150,00; ANVISA R$ 1.350,00 (450 × 3 produtos, uma vez cada); **total R$ 42.500,00** | Aprovado |
| 4 | Proposta revisada | Estado passou a **`revisada`** sem exigir lote/validade das amostras | Aprovado |
| 5 | Envio registrado | **ENV-2026-0012** · WhatsApp · Maria — Gerente de Compras (+55 11 99311-7171) · 25/11/2026 | Aprovado |
| 6 | Folder | **FOLD-2026-0011** montado com 111 produtos aprovados; **5 pendências de conteúdo** com dono; aprovação bloqueada (correto) | Aprovado |
| 7 | Trilha exportada | **14 eventos** do pedido em CSV sanitizado (sem nome de pessoa, só ID), incluindo os 4 de envio | Aprovado |

## 4. Defeitos reais encontrados pelo ensaio e corrigidos

### 4.1 Ordem do processo invertida — amostra bloqueava a proposta (v0.0.216)

**Sintoma:** o botão *Marcar como revisada* ficava desabilitado e o hook recusava a revisão, porque `amostras_incompletas` nascia com `bloqueia_envio: true`.

**Causa:** eu tratei lote e validade como pré-requisito da proposta. São dado de produção, que só existe **depois** de o cliente aprovar os valores — a ordem do processo comercial ficou ao contrário.

**Correção:** as três pendências de amostra passaram a **alerta informativo**, com o momento em que o dado nasce. A tabela continua no documento. **Nada mais foi afrouxado:** condições fora do padrão, serviço sem regra e linha incompleta seguem bloqueando. **Migration 0043** aplicou a mesma correção nas propostas já gravadas.

### 4.2 Ações de rascunho desapareciam sem explicação (v0.0.217)

**Sintoma:** o champion não encontrou *Alterar condições de pagamento*, *Registrar amostras aprovadas* nem *Marcar como revisada* — e anexou print da tela.

**Causa:** os três botões só existem em proposta `rascunho`; **eu mesmo** revisara a proposta na etapa 4 e não reescrevi o roteiro. **Falha de roteiro, não de funcionalidade.**

**Correção:** a tela passou a explicar que a proposta está revisada, por que as ações de rascunho não aparecem e onde fica a ação válida (*Criar nova versão*).

### 4.3 Envio duplicado aceito pela mesma pessoa (v0.0.218)

**Sintoma:** dois registros de envio da mesma proposta para a mesma cliente.

**Causa:** a idempotência compara `documento + versão + destinatário`, e **destinatário é texto livre**: "Maria Erotides" e "Maria — Gerente de Compras (+55 11 99311-7171)" passaram como destinatários distintos.

**Correção imediata:** ação **`marcar_duplicado`** (motivo ≥ 10, fail-closed, auditada), que marca sem apagar. **ENV-2026-0011** marcado; o válido é **ENV-2026-0012**. A regra de idempotência em si **não foi corrigida** — ver seção 7.

### 4.4 O envio não aparecia na trilha do pedido (v0.0.219)

**Sintoma:** filtrando a Trilha pelo pedido, 10 eventos e **nenhum de envio**.

**Causa:** o hook gravava o pedido no registro do envio, mas **não** no campo que a Trilha filtra (`pedido_id_ref`).

**Correção:** hook passa a gravar a referência; **migration 0044** preencheu os antigos ligando evento→envio por **evidência de instante**, exigindo no `registrar_envio` que o motivo nomeasse o documento.

**Resultado:** **8 de 28** preenchidos; **20 intactos** por ambiguidade real (quatro ou cinco eventos de TDD no mesmo segundo). Só a referência foi escrita — autor, hora, antes/depois, motivo e resultado **nunca** foram tocados. **Prova em tela:** 10 → **14 eventos**.

## 5. Evidência automática

- Migrations **0043** e **0044** conferidas na lista do schema como `applied`.
- Proposta 2026-0077: `pendencias_envio = []`, `estado = revisada`, `revisado_em` 25/11 17:21.
- Envio **ENV-2026-0012** gravado com documento, versão 1, canal, destinatário, pedido e autor.
- Evento `marcar_duplicado` na auditoria com resultado `sucesso` e motivo.
- Prova negativa: motivo curto → **400**; ação inexistente (`apagar_envio`) → **400** com a lista de ações válidas.
- CSV pela tela: 10 → **14 linhas** depois da correção.

## 6. O que a demonstração provou

O processo inteiro funciona num caso real, com rastro: pedido → produto vinculado com aprovação → proposta montada pelo servidor com preço de catálogo → revisão → envio registrado → folder montado → trilha exportável. **As seis decisões do champion foram implementadas no mesmo dia**, e cada uma nasceu de algo que ele viu na tela.

## 7. Pendências que seguem abertas

| # | Pendência | Dono | Por que não foi resolvida |
|---|---|---|---|
| P1 | **Conteúdo dos 5 blocos do folder** (`capa`, `linhas`, `produtos_destaque`, `alegacoes`, `contato`) | Marketing · Produtos/Comercial · Regulatório/Qualidade | Material da Florus. Sem texto e fonte o folder monta e lista o que falta, mas não aprova |
| P2 | **Amostras reais da Maria** (lote e validade das 3) | Vendedor, no aceite | O dado nasce na produção, **depois** de a cliente aprovar os valores e testar |
| P3 | **Idempotência de envio por texto livre** | Consultor + champion | Destinatário escrito de outro jeito continua passando como novo. Saídas em avaliação: vincular ao contato do pedido, ou comparar por telefone/e-mail. **Não corrigido** |
| P4 | **20 eventos de envio de TDD sem referência** | Interno | Ambíguos por construção. Preferi a lacuna visível ao palpite |
| P5 | Conteúdo dos 3 blocos complementares da proposta | Fábio · Regulatório · Marketing | Herdada da T-F3-002; aparece como alerta, não bloqueia |

## 8. Aprendizados

1. **Roteiro escrito antes de mudar o estado do objeto é roteiro morto.** Entreguei um passo pedindo botões que eu mesmo havia feito desaparecer. Antes de entregar roteiro, reabrir a tela **no estado atual**.
2. **Antes de transformar controle em pré-requisito, perguntar em que momento da vida do vendedor aquele dado nasce.** Lote de amostra não existe antes da produção.
3. **Idempotência por texto livre não é idempotência.** Campo digitado à mão precisa de chave estável.
4. **Trilha que não filtra o fato principal não é trilha.** O envio é o ato que o cliente percebe.
5. **Em dado ambíguo, a lacuna honesta vale mais que o preenchimento.**

## 9. Arquivos tocados

| Arquivo | Mudança |
|---|---|
| `pocketbase/hooks/montagem_proposta.js` | Pendências de amostra deixam de bloquear revisão e envio |
| `pocketbase/hooks/registro_envios.js` | Nova ação `marcar_duplicado`; grava `pedido_id_ref` na auditoria |
| `pocketbase/migrations/0043_amostra_nao_bloqueia_proposta.js` | Corrige propostas já gravadas |
| `pocketbase/migrations/0044_trilha_referencia_envio.js` | Preenche a referência nos eventos de envio existentes |
| `src/components/PropostaPainel.tsx` | Explica por que as ações de rascunho não aparecem em proposta revisada |

**Versões:** v0.0.216 (ordem da amostra) · v0.0.217 (explicação na tela) · v0.0.218 (`marcar_duplicado`) · v0.0.219 (trilha do envio).
