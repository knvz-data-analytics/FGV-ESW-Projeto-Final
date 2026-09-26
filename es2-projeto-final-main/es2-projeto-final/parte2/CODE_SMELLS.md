# Catálogo de Code Smells, Tarefa 2.1

Arquivo de hotspot escolhido: **`flaskbb/forum/views.py`** (1328 linhas, o maior dentro do escopo de `forum/`, já trabalhado na Parte 1).

7 smells encontrados (mínimo pedido: 6).

---

## 1. Long Method + Switch Statements, `ManageForum.post()`

**Localização:** `flaskbb/forum/views.py:412-538`

```python
    # TODO(anr): Clean this up. @_@
    def post(self, forum_id: int, slug: str | None = None):  # noqa: C901
        forum_instance, __ = Forum.get_forum(forum_id=forum_id, user=real(current_user))
        mod_forum_url = url_for(
            "forum.manage_forum", forum_id=forum_instance.id, slug=forum_instance.slug
        )
        ids = request.form.getlist("rowid")
        tmp_topics = (
            db.session.execute(db.select(Topic).where(Topic.id.in_(ids)))
            .scalars()
            .all()
        )
        if not len(tmp_topics) > 0:
            flash(...)
            return redirect(mod_forum_url)

        if "lock" in request.form:
            ...
        elif "unlock" in request.form:
            ...
        elif "highlight" in request.form:
            ...
        elif "trivialize" in request.form:
            ...
        elif "delete" in request.form:
            ...
        elif "move" in request.form:
            ...  # 20 linhas, com sub-validação própria
        elif "hide" in request.form:
            ...
        elif "unhide" in request.form:
            ...
        else:
            flash(_("Unknown action requested"), "danger")
            return redirect(mod_forum_url)
```

**Por que é um smell:** 126 linhas, 9 caminhos de execução (8 ações + "unknown"), decisão feita inteiramente por `if/elif` sobre chaves de `request.form`. O próprio autor original sinaliza a dívida técnica com o comentário `# TODO(anr): Clean this up. @_@` e o `# noqa: C901` (supressão explícita do aviso de complexidade ciclomática do linter, ou seja, o time já sabia que o método está complexo demais e decidiu conviver com isso). Método difícil de ler, testar e estender (adicionar uma 9ª ação exige mexer no mesmo bloco gigante).

---

## 2. Duplicated Code, ramos de ação em lote dentro de `ManageForum.post()`

**Localização:** `flaskbb/forum/views.py:436-534`

```python
        if "lock" in request.form:
            changed = do_topic_action(
                topics=tmp_topics, user=real(current_user), action="locked", reverse=False
            )
            flash(_("%(count)s topics locked.", count=changed), "success")
            return redirect(mod_forum_url)

        elif "unlock" in request.form:
            changed = do_topic_action(
                topics=tmp_topics, user=real(current_user), action="locked", reverse=True
            )
            flash(_("%(count)s topics unlocked.", count=changed), "success")
            return redirect(mod_forum_url)
        # ... o mesmo padrão se repete para highlight/trivialize/delete/hide/unhide
```

**Por que é um smell:** 6 dos 8 ramos (`lock`, `unlock`, `highlight`, `trivialize`, `delete`, `hide`, `unhide`, 7, na verdade) têm exatamente a mesma forma: chamar `do_topic_action(...)`, formatar uma mensagem de `flash`, redirecionar. Só o `action`, o `reverse` e o texto do `flash` mudam. É duplicação clássica, qualquer mudança no padrão (ex.: logar a ação, ou tratar erro de `do_topic_action`) precisa ser replicada em 7 lugares.

---

## 3. Duplicated Code (com bug real), pares Hide/Unhide de Tópico e Post

**Localização:** `flaskbb/forum/views.py:1061-1143` (classes `HideTopic`, `UnhideTopic`, `HidePost`, `UnhidePost`)

```python
class HidePost(MethodView):
    def post(self, post_id: int):
        post = first_or_404(db.select(Post).where(Post.id == post_id))
        if not Permission(Has("makehidden"), IsAtleastModeratorInForum(forum=post.topic.forum)):
            flash(...)
            return redirect(post.topic.url)
        if post.hidden:
            flash(_("Post is already hidden"), "warning")
            return redirect(post.topic.url)          # <- tem "return"
        post.hide(current_user)
        ...

class UnhidePost(MethodView):
    def post(self, post_id: int):
        post = first_or_404(db.select(Post).where(Post.id == post_id))
        if not Permission(Has("makehidden"), IsAtleastModeratorInForum(forum=post.topic.forum)):
            flash(...)
            return redirect(post.topic.url)
        if not post.hidden:
            flash(_("Post is already unhidden"), "warning")
            redirect(post.topic.url)                  # <- SEM "return"! (linha 1138)
        post.unhide()
        post.save()
        flash(_("Post unhidden"), "success")
        return redirect(post.topic.url)
```

**Por que é um smell, e por que é grave:** as 4 classes replicam o mesmo esqueleto (checar permissão, checar estado atual, mutar, salvar, redirecionar), copiado e colado 4 vezes com pequenas variações. Essa duplicação já causou um **bug real**: em `UnhidePost.post()` (linha 1138), o `redirect(...)` do caso "já está oculto" não tem `return`, ao contrário do `HidePost` (linha 1109), que serviu de modelo e tem o `return` correto. Na prática, chamar "unhide" em um post que já está visível ainda executa `post.unhide()`/`post.save()` e mostra a flash de sucesso logo depois da de aviso, o guard clause não impede nada. Exatamente o tipo de divergência que duplicação de código convida a acontecer.

---

## 4. Duplicated Code, toggles de um campo booleano em tópico

**Localização:** `flaskbb/forum/views.py:806-887` (classes
`LockTopic`, `UnlockTopic`, `HighlightTopic`, `TrivializeTopic`)

```python
class LockTopic(MethodView):
    decorators = [login_required, allows.requires(IsAtleastModeratorInForum(), on_fail=FlashAndRedirect(
        message=_("You are not allowed to lock this topic"), level="danger",
        endpoint=lambda *a, **k: current_topic.url))]

    def post(self, topic_id: int, slug: str | None = None):
        topic = first_or_404(db.select(Topic).where(Topic.id == topic_id), True)
        topic.locked = True
        topic.save()
        return redirect(topic.url)

class UnlockTopic(MethodView):
    decorators = [...]  # idêntico, só troca a mensagem

    def post(self, topic_id: int, slug: str | None = None):
        topic = first_or_404(db.select(Topic).where(Topic.id == topic_id), True)
        topic.locked = False
        topic.save()
        return redirect(topic.url)
```

**Por que é um smell:** 4 classes inteiras (cada uma com ~13 linhas de decorator + 5 linhas de `post()`) são idênticas exceto por qual atributo booleano setam (`locked`/`important`) e para qual valor (`True`/`False`). É um caso claro de "Replace Conditional/Parallel Classes with algo mais genérico", as 4 poderiam colapsar em uma única view parametrizada pelo atributo e valor.

---

## 5. Duplicated Code, resolução de ordenação em `MemberList`

**Localização:** `flaskbb/forum/views.py:653-667` e `680-694`

```python
        # em MemberList.get() (653-667) E em MemberList.post() (680-694),
        # exatamente o mesmo bloco:
        if order_by == "asc":
            order_func = asc
        else:
            order_func = desc

        if sort_by == "reg_date":
            sort_obj = User.id
        elif sort_by == "post_count":
            sort_obj = User.post_count
        else:
            sort_obj = User.username
```

**Por que é um smell:** os 15 mesmos linhas aparecem, char por char, em `get()` e em `post()`. Qualquer novo critério de ordenação (ex.: por e-mail) precisa ser adicionado nos dois lugares, e é fácil esquecer um deles, como já vimos acontecer no item 3.

---

## 6. Long Method com duas responsabilidades, `MarkRead.post()`

**Localização:** `flaskbb/forum/views.py:947-1007`

```python
    def post(self, forum_id: int | None = None, slug: str | None = None):
        # Mark a single forum as read
        if forum_id is not None:
            forum_instance = first_or_404(...)
            forumsread = db.session.execute(...).scalar()
            db.session.execute(db.delete(TopicsRead)...)
            if not forumsread:
                forumsread = ForumsRead()
                ...
            forumsread.last_read = time_utcnow()
            forumsread.cleared = time_utcnow()
            db.session.add(forumsread)
            db.session.commit()
            flash(...)
            return redirect(forum_instance.url)

        # Mark all forums as read
        db.session.execute(db.delete(ForumsRead)...)
        db.session.execute(db.delete(TopicsRead)...)
        forums = db.session.execute(db.select(Forum)).scalars()
        forumsread_list = []
        for forum_instance in forums:
            forumsread = ForumsRead()
            ...
        db.session.add_all(forumsread_list)
        db.session.commit()
        flash(_("All forums marked as read."), "success")
        return redirect(url_for("forum.index"))
```

**Por que é um smell:** 60 linhas, e os próprios comentários ("Mark a single forum as read" / "Mark all forums as read") já admitem que são duas operações de negócio diferentes, cada uma manipulando `ForumsRead`/`TopicsRead` diretamente via SQLAlchemy, espremidas num único método por causa de um parâmetro opcional (`forum_id`). Duas responsabilidades, um método.

---

## 7. Data Clumps, par `topic_id`/`slug` (e `forum_id`/`slug`) repetido

**Localização:** assinaturas de `get`/`post` em pelo menos 15 métodos ao longo do arquivo, por exemplo: `ViewTopic.get/post` (linhas 212, 255), `NewPost.get/post` (558, 569), `EditTopic.get/post` (334, 343), `DeleteTopic.post` (800), `LockTopic.post` (820), `UnlockTopic.post` (841), `HighlightTopic.post` (862), `TrivializeTopic.post` (883), `TrackTopic.post` (1034), `UntrackTopic.post` (1054)...

```python
def post(self, topic_id: int, slug: str | None = None):
    topic = first_or_404(db.select(Topic).where(Topic.id == topic_id), True)
    ...
```

**Por que é um smell:** o par `(topic_id, slug)` sempre anda junto e sempre serve só para uma coisa, resolver o `Topic`, mas é repassado como dois parâmetros primitivos soltos em vez de, por exemplo, um resolvedor único de rota que já entrega o `Topic`. É "Primitive Obsession"/"Data Clumps" clássico: o dado que devia ser um conceito (`o tópico desta requisição`) está espalhado em primitivos por todo o módulo.