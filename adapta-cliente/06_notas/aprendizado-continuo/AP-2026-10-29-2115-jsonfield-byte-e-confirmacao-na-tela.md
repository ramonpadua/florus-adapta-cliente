# AP-2026-10-29-2115 — JSONField chega como []byte do Go; e a confirmação precisa morar onde o olho está

- Status: candidato
- Escopo: projeto do cliente · plataforma (Skip/JSVM)
- Task/SPEC: T-F3-002 · SPEC-3-001 (CA-3-004/CA-3-006)

## Sinal 1 — JSONField em hook JSVM

Duas versões seguidas (v0.0.173 e v0.0.174) até a aprovação de bloco voltar a funcionar.

- **Causa raiz:** campo `type: 'json'` chega ao hook como `[]byte` do Go.
  `Array.isArray([]byte)` é falso, e `JSON.stringify([]byte("[]"))` devolve `"[91,93]"`
  (os bytes do colchete), não `"[]"`. A lista VAZIA de divergências era lida como
  "tem divergência" e bloqueava 100% das aprovações.
- **Regra reutilizável:** antes de decidir qualquer coisa sobre um campo JSON em hook,
  serializar e, se o texto casar com `/^\[\d+(,\d+)*\]$/`, **decodificar os bytes**
  (`String.fromCharCode`) de volta para a string JSON. Só então comparar.
- **Sintoma que denuncia:** a regra "funciona no papel" mas falha para o valor neutro
  (`[]`, `{}`, `null`) — o caso vazio é o que quebra primeiro.
- **Onde mais aplicar:** todo hook que ler `pendencias`, `divergencias`, `produtos`,
  `condicoes`, `eventos_integracao` e qualquer campo JSON da plataforma.

## Sinal 2 — Roteiro de teste com dois botões e sem nomeá-los

O champion travou no passo 9 do roteiro da T-F3-002 e voltou três vezes ("Não entendi
com clareza", "Não entendi o que eu tenho que fazer", "explique detalhadamente").

- **Causa raiz:** o formulário tem **dois** botões (`Salvar rascunho` e `Salvar e aprovar`),
  com regras diferentes, e o roteiro dizia apenas "salve". Além disso, o rótulo do campo
  era "Motivo (mín. 10 para aprovar)" — visível também no caminho que não exige motivo.
- **Regra reutilizável:** todo passo de roteiro nomeia o rótulo EXATO do botão e diz quais
  campos aquela ação exige, o que pode ficar em branco e o que a outra ação faria.
- **Regra reutilizável 2:** a trava prova-se por TDD; o teste humano deve provar o que só
  o humano vê (o estado das telas por papel). Não pedir para digitar dado falso em bloco
  real — contamina conteúdo e, em campo Select, não há como desfazer pela tela.
- **Regra reutilizável 3:** campo de seleção sempre precisa de caminho de volta (limpar).
  O Select de Fonte não tinha — defeito real apontado pelo cliente, corrigido em v0.0.176.

- Confiança: alta — os dois sinais foram reproduzidos, corrigidos e provados.
- Privacidade: sem segredo, dado pessoal ou conteúdo bruto.
