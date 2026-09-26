# Refatorações Aplicadas — Tarefa 2.3

4 commits aplicados em `flaskbb/forum/views.py` (fork), na ordem do
`parte2/PLANO_REFACTORING.md` — do menor pro maior risco. Suíte
completa (`uv run pytest`) verde após cada um: 249 → 249 → 250 → 258
passed (1 skipped, pré-existente e sem relação com esta parte),
rodada 3x seguidas pra confirmar estabilidade.

> commitar no seu fork (`git log --oneline -4`).

---

## Commit 1 — `0bea3d2`

**Mensagem:** `refactor(forum): extract method de MemberList._resolve_sort`
**Smell tratado:** #5 (ordenação duplicada em `MemberList`)
**Transformação:** Extract Method

**Antes:** `get()` e `post()` de `MemberList` repetiam, cada um, as
mesmas 15 linhas resolvendo `order_func`/`sort_obj` a partir de
`sort_by`/`order_by`.

**Depois:** os dois métodos chamam `self._resolve_sort(sort_by,
order_by)`, um novo método que centraliza essa lógica.

```python
def _resolve_sort(self, sort_by: str, order_by: str):
    order_func = asc if order_by == "asc" else desc
    if sort_by == "reg_date":
        sort_obj = User.id
    elif sort_by == "post_count":
        sort_obj = User.post_count
    else:
        sort_obj = User.username
    return order_func, sort_obj
```

---

## Commit 2 — `f21be19`

**Mensagem:** `refactor(forum): extract method de _set_topic_flag em Lock/Unlock/Highlight/TrivializeTopic`
**Smell tratado:** #4 (4 classes quase idênticas de toggle de flag)
**Transformação:** Extract Method

**Antes:** `LockTopic`, `UnlockTopic`, `HighlightTopic` e
`TrivializeTopic` repetiam, cada uma, a sequência buscar tópico →
setar um atributo booleano → salvar → redirecionar.

**Depois:** as 4 delegam para uma função de módulo
`_set_topic_flag(topic_id, attr_name, value)`, e cada `post()` cai
para uma linha:

```python
def _set_topic_flag(topic_id: int, attr_name: str, value: bool) -> Topic:
    topic = first_or_404(db.select(Topic).where(Topic.id == topic_id), True)
    setattr(topic, attr_name, value)
    topic.save()
    return topic

# em cada classe, por exemplo LockTopic:
def post(self, topic_id: int, slug: str | None = None):
    topic = _set_topic_flag(topic_id, "locked", True)
    return redirect(topic.url)
```

---

## Commit 3 — `4cf324e`

**Mensagem:** `refactor(forum): extract method em Hide/UnhideTopic/Post e corrige bug de return ausente em UnhidePost`
**Smell tratado:** #3 (duplicação com bug real em Hide/Unhide)
**Transformação:** Extract Method

**Antes:** as 4 classes (`HideTopic`, `UnhideTopic`, `HidePost`,
`UnhidePost`) repetiam a checagem de permissão
`Permission(Has("makehidden"), IsAtleastModeratorInForum(...))`, e
`HidePost`/`UnhidePost` repetiam também a checagem "já está no
estado desejado". Nessa segunda checagem, `UnhidePost.post()` tinha
um `redirect(...)` **sem `return`** — bug real: desocultar um post
já visível reprocessava a ação e disparava uma segunda mensagem de
sucesso contraditória.

**Depois:** dois helpers de módulo (`_require_hide_permission` e
`_redirect_if_already`) centralizam essas duas checagens; como o
guard clause agora sempre vem de um único lugar que sempre retorna,
o bug deixa de existir estruturalmente — não tem mais como uma das 4
chamadas "esquecer" o `return`.

```python
def _redirect_if_already(condition: bool, message: str, redirect_url: str):
    if condition:
        flash(message, "warning")
        return redirect(redirect_url)
    return None

# em UnhidePost.post():
already = _redirect_if_already(
    not post.hidden, _("Post is already unhidden"), post.topic.url
)
if already:
    return already
```

**Teste de regressão adicionado:**
`tests/unit/forum/test_forum_views_refactor.py` — verifiquei que ele
**falha** contra o código antigo (`['warning', 'success']`, as duas
mensagens contraditórias) e **passa** contra o código novo (só
`['warning']`), provando a correção do bug.

---

## Commit 4 — `a796db7`

**Mensagem:** `refactor(forum): extract method + tabela de despacho em ManageForum.post`
**Smell tratado:** #2 (Long Method / Switch Statements em `ManageForum.post`) — e, de quebra, #1 do catálogo
**Transformação:** Extract Method

**Antes:** `post()` tinha 126 linhas e 9 caminhos (`if`/`elif`
encadeados), com `# noqa: C901` e `# TODO(anr): Clean this up. @_@`
reconhecendo a complexidade. 7 dos 9 ramos (`lock`, `unlock`,
`highlight`, `trivialize`, `delete`, `hide`, `unhide`) tinham
exatamente a mesma forma: `do_topic_action(...)` + `flash(...)` +
`redirect(...)`.

**Depois:** uma tabela `_BULK_ACTIONS` mapeia cada ação simples para
`(campo, reverse, mensagem)`, e um helper `_apply_bulk_topic_action`
aplica qualquer uma delas. `post()` caiu de 126 para ~45 linhas; o
ramo `move` (que tem validação e permissão extras) continua
explícito, fora da tabela.

```python
_BULK_ACTIONS = {
    "lock": ("locked", False, lambda count: _("%(count)s topics locked.", count=count)),
    "unlock": ("locked", True, lambda count: _("%(count)s topics unlocked.", count=count)),
    # ... highlight, trivialize, delete, hide, unhide
}

def _apply_bulk_topic_action(action: str, topics: list, mod_forum_url: str):
    field, reverse, message_for = _BULK_ACTIONS[action]
    changed = do_topic_action(topics=topics, user=real(current_user), action=field, reverse=reverse)
    flash(message_for(changed), "success")
    return redirect(mod_forum_url)

# em ManageForum.post():
for action in _BULK_ACTIONS:
    if action in request.form:
        return _apply_bulk_topic_action(action, tmp_topics, mod_forum_url)
```

**Testes adicionados:** `tests/unit/forum/test_manage_forum.py` — 9
casos cobrindo as 8 ações (lock/unlock/highlight/trivialize/delete/
hide/unhide/move) mais "ação desconhecida" e "nenhum tópico
selecionado". `ManageForum.post()` tinha 0% de cobertura ao final da
Parte 1; era a refatoração de maior risco do plano, então preferi
validar automaticamente em vez de só manualmente.

---

## Nota sobre string dinâmica de mensagem (i18n)

Nos commits 3 e 4, tomei cuidado para que cada string traduzível
continuasse aparecendo como um literal `_("...")` no código-fonte
(ex.: `_("You do not have permission to hide this topic")` é passado
como argumento já traduzido para `_require_hide_permission`, em vez
de ser composto dinamicamente dentro do helper). Isso preserva a
extração de strings do `pybabel extract` — compor a mensagem
dinamicamente (ex.: `_("...to %(action)s this...", action=verbo)`)
quebraria a extração e a tradução para outros idiomas.