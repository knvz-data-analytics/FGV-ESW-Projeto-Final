# Plano de Refactoring, Tarefa 2.2

4 smells escolhidos do `parte2/CODE_SMELLS.md` para tratar nesta Parte 2, em ordem de aplicação (do mais simples/seguro pro mais arriscado).

---

## 1. Smell #5, Ordenação duplicada em `MemberList` (linhas 653-667 e 680-694)

**Refatoração:** Extract Method, criar um método auxiliar `_resolve_sort(sort_by, order_by) -> (order_func, sort_obj)` e chamá-lo tanto em `MemberList.get()` quanto em `MemberList.post()`.

**Resultado esperado:** os dois métodos passam a chamar a mesma função para decidir ordenação, eliminando a cópia idêntica de 15 linhas entre `get()` e `post()`.

**Riscos:** mínimos, é lógica pura, sem efeito colateral. Único cuidado é preservar exatamente os valores-padrão atuais (`sort_by="reg_date"`, `order_by="asc"`) para não mudar o comportamento quando os parâmetros de query não são enviados.

---

## 2. Smell #4, Toggles de campo booleano (`LockTopic`, `UnlockTopic`, `HighlightTopic`, `TrivializeTopic`, linhas 806-887)

**Refatoração:** Extract Method, criar um método auxiliar `_set_topic_flag(topic_id, attr_name, value)` que busca o tópico, seta o atributo, salva e devolve o tópico; cada `post()` das 4 classes passa a ter uma única linha chamando esse helper com o par (atributo, valor) correspondente.

**Resultado esperado:** cada uma das 4 classes cai de ~5 linhas de lógica repetida (busca + set + save) para uma única chamada ao helper, sem duplicar mais a sequência "buscar tópico → mutar → salvar → redirecionar".

**Riscos:** usar `setattr` dinâmico com uma string de nome de atributo reduz a clareza/checagem estática se mal feito, mitigar passando o nome do atributo como parâmetro explícito e documentado (não aceitar qualquer string arbitrária vinda de fora da própria classe). As decorações de permissão (`IsAtleastModeratorInForum`) e as mensagens de erro continuam específicas de cada classe, então só o corpo do `post()` é unificado, não as classes inteiras.

---

## 3. Smell #3, Duplicação (com bug) em `HideTopic`/`UnhideTopic`/`HidePost`/`UnhidePost` (linhas 1061-1143)

**Refatoração:** Extract Method, criar um método auxiliar `_set_hidden(entity, hide, redirect_url)` encapsulando o trio "checar permissão → checar se já está no estado desejado → mutar → salvar", usado pelas 4 views. Como esse é o smell que já **causa um bug** (falta de `return` em `UnhidePost`), a extração corrige o bug automaticamente: o guard clause do método único sempre retorna, não tem como uma das 4 chamadas esquecer o `return` de novo.

**Resultado esperado:** as 4 classes passam a delegar a lógica de mudança de estado para um único método auxiliar, eliminando a duplicação **e** corrigindo o bug do `redirect()` sem `return` em `UnhidePost.post()`.

**Riscos:** o de maior atenção dos quatro depois do `ManageForum`. `Topic` e `Post` têm particularidades diferentes na hora de decidir para onde redirecionar (`HidePost`/`UnhidePost` verificam `post.is_first_post()` e a permissão `viewhidden` pra decidir entre `post.topic.url` e `post.topic.forum.url`; `HideTopic`/`UnhideTopic` não têm essa checagem). É preciso generalizar sem perder essa diferença, resolver passando a URL de redirecionamento já calculada pelo chamador, em vez de tentar adivinhar dentro do helper. Como esse smell já esconde um bug, a suíte de testes da Parte 1 (`test_forum_views.py`, que já cobre `LockTopic` e o caso de borda de `HidePost`) deve continuar verde, e vale adicionar manualmente um teste que hoje falharia (unhide de um post já visível não deveria re-processar) como parte da Tarefa 2.3, para provar a correção.

---

## 4. Smell #2, 8 ramos repetidos em `ManageForum.post()` (linhas 412-538)

**Refatoração:** Extract Method, extrair um método auxiliar `_apply_bulk_action(action, topics)` que recebe o nome da ação já identificada e aplica `do_topic_action(...)` + monta a mensagem de `flash`, usando uma pequena tabela (dict) que mapeia `nome_da_ação → (action, reverse, mensagem)` para os 7 ramos que seguem o mesmo formato (`lock`, `unlock`, `highlight`, `trivialize`, `delete`, `hide`, `unhide`). O ramo `move` fica de fora da tabela, por ter validação e permissão extra própria.

**Resultado esperado:** `ManageForum.post()` cai de 126 linhas para menos de 50: identifica qual ação foi enviada, consulta a tabela e delega a um único método auxiliar, o ramo `move` continua explícito por ser genuinamente diferente dos outros.

**Riscos:** o de maior risco do plano, por ser o método mais usado (qualquer ação de moderação em massa passa por ele) e o mais complexo. Um erro na tabela de mapeamento (ação → campo/reverse/ mensagem) quebraria silenciosamente uma ação inteira sem lançar exceção. Mitigação: aplicar essa refatoração por último (depois das outras três, com a suíte já validando o resto do arquivo), rodar `uv run pytest` após cada sub-passo, e testar manualmente cada uma das 8 ações uma vez antes do commit final, já que a Parte 1 não cobre `ManageForum.post()` com testes automatizados (é a maior lacuna de cobertura apontada em `COBERTURA_FINAL.md`).

---

## Ordem de aplicação e commits

Aplicar do menor pro maior risco: **(1) MemberList → (2) toggles de flag → (3) hide/unhide → (4) ManageForum**. Cada um vira um commit próprio na Tarefa 2.3, com a suíte verde entre um commit e o seguinte, se algo quebrar, o problema fica isolado a uma única refatoração.