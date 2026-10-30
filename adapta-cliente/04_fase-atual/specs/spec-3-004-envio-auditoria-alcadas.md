# SPEC-3-004 — Envio com registro, auditoria, alçadas e fail-closed

**Fase:** 3
**Status:** liberada
**Dono:** vendedor (envia as suas); gestor/admin (alçadas e auditoria); champion Fábio (regras de envio)
**Origem no escopo:** Fase 3 — "Proposta, folder, conteúdo aprovado, versão e envio"; padrões de auditoria/RLS da Fase 1 (SPEC-1-003) e de fail-closed da Fase 2 (SPEC-2-004)
**Degrau da solução:** envio registrado dentro do sistema Skip existente; canal de entrega (e-mail/WhatsApp/link) é insumo do champion na task — sem integração externa presumida.

## Contexto e decisões fechadas

- **Estado atual:** envio de proposta/folder é manual e sem registro — não há rastreio de quem enviou o quê, para quem, em qual versão, nem recuperação após falha.
- **Estado desejado:** envio como ato registrado no sistema: ator, papel, data, destinatário, versão enviada; negativa de envio de versão não aprovada; reenvio idempotente; recuperação após falha com tentativa preservada e reconciliação visível.
- **Decisões já fechadas:** proposta/folder só enviam em estado `revisada` (prova negativa de rascunho); auditoria server-side com ator, papel, antes→depois, motivo, versão e resultado; fail-closed — ação sem alçada definida é negada por padrão (padrão T-F2-007); vendedor negado com motivo do servidor.
- **Insumos embutidos (não travam o início):** canal de envio (e-mail/WhatsApp/link) e regras de destinatário — o champion entrega dentro da task T-F3-006; matriz de alçadas de aprovar conteúdo e enviar/reenviar — insumo da task T-F3-007.

## Resultado observável

O vendedor envia a proposta/folder revisada com registro completo do ato; tentativa de envio de rascunho é negada com motivo do servidor e auditada; reenvio idempotente (não duplica registro); falha preserva a tentativa e permite reconciliação; gestor exporta a trilha de envios.

## Limites e dependências

- **Inclui:** registro do envio (ator, papel, data, destinatário, versão); auditoria server-side; negativas com motivo; reenvio idempotente; recuperação após falha; fila de pendências de envio; exportação CSV da trilha sanitizada.
- **Fora de escopo:** automação de follow-up do envio (Fase 4); alterar Cotação, campos travados ou coleções das Fases 1–2 (exceto leitura).
- **Entradas e pré-condições:** proposta/folder `revisada` (SPEC-3-002/003); papéis da Fase 1; matriz de alçadas (insumo embutido).
- **Saídas/artefatos:** coleção `envios` com trilha; relatório de cenários; CSV da trilha.
- **Dependências e responsáveis:** champion decide canal e regras; vendedor envia; gestor/admin audita e exporta.
- **Atores e permissões mínimas:** vendedor envia as próprias propostas/folders; gestor/admin tudo e exportação; sem alçada definida → negado (fail-closed).
- **Superfícies afetadas:** nova coleção `envios` + hooks; leitura de `propostas` e `folders`.
- **Risco e plano B:** falha no canal externo; plano B é registro local do envio com pendência de reconciliação (padrão T-F1-005/T-F1-008).
- **Rollback:** envio não é revertido (ato consumado é histórico); correção é nova versão + reenvio registrado.

## Dados e integrações

| Origem/destino | Fonte de verdade | Campos/contrato | Autenticação/permissão | Timeout/retry/idempotência | Tratamento de erro |
|---|---|---|---|---|---|
| Proposta/folder → envio | Estado `revisada` somente | `proposta_id`/`folder_id` + versão | RLS Fase 3 | Idempotência por destinatário+versão | Rascunho → negativa com motivo |
| Envio → registro | Coleção `envios` | Ator, papel, data, destinatário, versão, resultado | Server-side hooks | Registro único por envio; retry não duplica | Falha preserva tentativa |
| Envio → auditoria | Trilha server-side | Ator, papel, antes→depois, motivo, versão, resultado | Gestor/admin leitura | Append-only | Nada é apagado |

| Regra de negócio | Condição | Ação/resultado | Exceção | Fonte |
|---|---|---|---|---|
| RN-3.013 | Envio de rascunho | Negado com motivo do servidor; evento auditado | — | Padrão Fase 1 |
| RN-3.014 | Envio sem alçada definida | Negado por padrão (fail-closed) | Alçada cadastrada na matriz | Padrão T-F2-007 |
| RN-3.015 | Reenvio do mesmo destinatário+versão | Idempotente: um registro, sem duplicação | — | Padrão T-F1-005 |
| RN-3.016 | Falha no envio | Tentativa preservada com estado de pendência; reconciliação visível | — | Padrão T-F1-008 |

## Fluxo e regras

1. Vendedor solicita o envio da proposta/folder `revisada`.
2. Sistema valida estado e alçada; negativa com motivo se rascunho ou sem permissão.
3. Envio registrado com ator, papel, data, destinatário e versão.
4. Reenvio idempotente; falha preserva tentativa e abre pendência de reconciliação.
5. Gestor exporta a trilha (CSV sanitizado, padrão T-F2-008).

| Cenário | Dado/condição | Resultado esperado | Caminho de erro/recuperação |
|---|---|---|---|
| Principal | Proposta `revisada` + alçada ok | Envio registrado completo | — |
| Limite | Rascunho ou sem alçada | Negativa com motivo; evento auditado | Correção pelo dono da alçada |
| Falha | Canal indisponível | Tentativa preservada; pendência de reconciliação | Reconciliação manual visível |

## Instruções de execução para o Ethos

1. **Ler antes de alterar:** esta SPEC; padrões de auditoria da Fase 1 e fail-closed da Fase 2.
2. **Alterar somente:** coleção `envios`, hooks, tela de envio/pendências/exportação.
3. **Não alterar:** coleções das Fases 1–2 (exceto leitura), Cotação, campos travados.
4. **Executar nesta ordem:** canal e regras (insumo) → modelar coleção → registro do envio → negativas e fail-closed → idempotência/recuperação → exportação → demonstração.
5. **Parar e pedir validação quando:** o champion não entregar canal/regras na task; falha exigir integração externa não autorizada.
6. **Estado válido ao parar:** tentativas preservadas, operação das Fases 1–2 intacta, pendências nomeadas.

## Checklist de execução

- [ ] Canal de envio e regras de destinatário definidos pelo champion.
- [ ] Coleção `envios` com registro completo do ato.
- [ ] Negativa de rascunho/sem alçada com motivo do servidor (prova negativa).
- [ ] Reenvio idempotente; recuperação após falha com tentativa preservada.
- [ ] Auditoria server-side + exportação CSV sanitizada.

## Critérios de aceite

- [ ] **CA-3-017:** envio registrado com ator, papel, data, destinatário e versão.
- [ ] **CA-3-018:** envio de rascunho é negado com motivo e auditado (prova negativa).
- [ ] **CA-3-019:** ação sem alçada definida é negada por padrão (fail-closed).
- [ ] **CA-3-020:** reenvio idempotente não duplica registro.
- [ ] **CA-3-021:** falha preserva a tentativa e permite reconciliação visível.
- [ ] **CA-3-022:** trilha exportável em CSV sanitizado (sem dado pessoal).

## TDD da SPEC

| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Enviar proposta hoje | Perguntar como o envio é registrado hoje | Sem registro/rastreio | Registro na task |
| GREEN | CP-F3-008/009 | Enviar proposta revisada; tentar enviar rascunho | Registro completo; negativa auditada | Relatório de cenários |
| REGRESSÃO | CP-F3-010/011/012 | Reenvio; falha simulada; sem alçada | Idempotência; reconciliação; fail-closed | Relatório de cenários |

## Tasks vinculadas

- T-F3-006 (onda 6)
- T-F3-007 (onda 7)
- T-F3-008 (onda 8)

## Emenda E-F3-20261030-01 (30/10/2026) — contrato de interação das tasks abertas

**Vale só para as tasks abertas da Fase 3** (T-F3-003 a T-F3-008 e a revisão do consultor). Não altera
nada das Fases 1 e 2, não reabre task concluída e não muda critério de aceite, prazo nem responsável.
A emenda existe porque o `04_fase-atual/fase.md` foi reescrito em linguagem leiga em 30/10/2026
(commit `176210e`) e o texto da task passou a ser o contrato que vale na conversa com o champion.

**Ordem da explicação, em qualquer task aberta:** primeiro o que **já existe** (e que não deve ser
pedido de novo), depois o que **vai existir no Skip**, depois **por que é útil**. Só então a pergunta.

**Coleta no Maestro:** uma pergunta por vez, no chat do Maestro, e **só sobre o que falta** — o que já
está nos documentos, nas respostas anteriores ou no sistema não é perguntado outra vez. Anexo que o
champion mandar fica no Maestro e não é repassado por WhatsApp nem por e-mail.

**Antes de construir:** conferir a fonte de cada informação e **pedir autorização** ao champion,
mostrando o que vai ser montado; sem o "ok" nada é construído. Informação sem fonte aprovada vira
pendência com nome e dono — nunca é preenchida no silêncio, e nenhuma regra, número, benefício ou
rótulo de tela é inventado (se o rótulo não foi conferido, a ação é descrita pelo resultado).

**Prova:** roteiro humano passo a passo com os rótulos como aparecem na tela e o resultado esperado de
cada passo; uma task por vez, com o aceite do champion antes da próxima. Nenhum envio a cliente real
sem confirmação na hora.

**Nesta SPEC (T-F3-006 a T-F3-008):** o canal de envio **já está decidido**: WhatsApp, com **3
vendedores e 1 número para cada um** — não se pergunta de novo. O que falta decidir é a **forma de
entrega**, uma vez, antes de construir: **(A) manual com registro** — o vendedor baixa o arquivo ou o
link, envia pelo WhatsApp e o sistema registra o ato, sem custo novo; ou **(B) API oficial do
WhatsApp** — o sistema envia sozinho, com custo por mensagem, credenciais e aprovação da Meta. Eu
explico o que cada uma exige e o champion autoriza. **O conector do Maestro não é a integração do
Skip**: ter um não dá o outro, e nada é presumido como integrado. Qualquer API, custo ou credencial
nova é **gate do consultor** antes de qualquer construção. As decisões RN-3.013 a RN-3.016 e os
critérios CA-3-017 a CA-3-022 continuam valendo sem alteração.
