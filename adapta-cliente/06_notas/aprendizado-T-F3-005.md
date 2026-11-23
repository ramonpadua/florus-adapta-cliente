# Aprendizado — T-F3-005 (folder)

## Padrões reutilizáveis confirmados

1. **Ler o schema da `auditoria` antes de escrever nela.** Os campos reais são `event_id`
   (obrigatório e único), `antes`, `depois`, `ator_papel`. Eu inventei `entidade` e
   `valor_anterior` e o CREATE passou a devolver **500** — `set` em campo inexistente lança, não
   ignora. Copiar a assinatura do hook que já funciona (`montagem_proposta.js`).
2. **Estado ≠ motivo do estado.** "Saiu de circulação" tem duas causas diferentes: substituído por
   versão nova, ou arquivado. Reutilizar `substituida` para as duas fez a tela afirmar
   "substituída por uma versão nova" sobre folders que ninguém substituiu. Marcador explícito
   (`substituido_por = 'ARQUIVADO'`) separa os dois sem criar estado novo no select.
3. **Seletor de tela lista o que está em circulação; histórico fica na tabela de versões.**
   Mesma regra já aplicada em Lista de Pedidos e Pipe (23/10). Repetir a regra em toda tela nova
   que tenha linhagem, **antes** de entregar.
4. **TDD que suja registro real precisa de restauração que o próprio hook permita.** Bloco
   aprovado não se edita no lugar (RN-3.003): a restauração tem de ser versão nova, e criar versão
   nova exige `conteudo_id` novo (`conteudo_id` é único). Improvisar restauração por PATCH falha.
5. **Ação de limpeza precisa ser idempotente e travar o menos possível.** Meu guard de "versão
   substituída não é editável" impedia justamente a ação de arquivar. Ações de ciclo de vida
   (arquivar) vêm **antes** dos guards de estado.
6. **Publicar arquivo no remoto: montar o payload com o arquivo RELIDO e conferir a string antes
   de enviar.** Publiquei texto provisório em `STATUS.md` duas vezes por digitar o conteúdo à mão
   no payload em vez de ler o arquivo.

## Riscos que este ciclo expôs

- **Concluir sem aceite explícito.** "Pode seguir" não é aceite; pedir a marcação no formulário
  custou uma rodada e evitou fechar a task sobre um teste que talvez não tivesse acontecido.
- **Revalidação de conclusão vale a pena.** Foi ela (não o TDD) que pegou o seletor poluído e o
  arquivado disfarçado de substituído — os dois eram de tela, e nenhum teste automatizado olhava
  para eles.
