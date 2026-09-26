## Propósito

O módulo `forum/` é o núcleo de domínio do FlaskBB: implementa a hierarquia categoria → fórum → tópico → post que estrutura qualquer fórum de discussão, junto com as ações de moderação sobre ela (trancar, destacar, ocultar, mover, deletar tópicos/posts), o rastreamento de leitura por usuário (o que cada um já leu ou não), a busca e a listagem de membros. Praticamente toda a experiência "pública" do fórum, o que um visitante ou moderador vê e faz ao navegar pelo site, passa por este módulo; os demais módulos (`user/`, `management/`, `auth/`) cuidam de contas e administração, mas o *conteúdo* do fórum em si vive aqui.

## Mapa dos arquivos principais

| Arquivo | Responsabilidade |
|---|---|
| `models.py` (1665 linhas) | Modelos SQLAlchemy `Category`, `Forum`, `Topic`, `Post`, `TopicsRead`, `ForumsRead`, `Report` e toda a regra de negócio associada (mover tópico, recalcular contadores, marcar como lido, ocultar/desocultar). |
| `views.py` (1335 linhas) | As ~30 `MethodView`s que atendem as rotas HTTP do fórum (ver tópico, responder, moderar, buscar, listar membros) e o registro dessas rotas via o hook de plugin `flaskbb_load_blueprints`. |
| `forms.py` (218 linhas) | Formulários WTForms para criar/editar tópicos e posts, reportar conteúdo e buscar (usuários e conteúdo geral). |
| `locals.py` (78 linhas) | Quatro `LocalProxy`s (`current_post`/`current_topic`/`current_forum`/`current_category`) que resolvem "qual post/tópico/fórum/categoria é este da requisição atual", em cascata. |
| `utils.py` (39 linhas) | Duas funções pequenas para decidir se um usuário anônimo precisa ser forçado a logar antes de acessar um fórum. |

## Pontos de entrada externos

- **Rotas HTTP** (todas registradas em `views.py:flaskbb_load_blueprints`, sob o prefixo `app.config["FORUM_URL_PREFIX"]`): ~30 rotas cobrindo navegação (`/`, `/category/<id>`, `/forum/<id>`, `/topic/<id>`), criação/edição de conteúdo (`/topic/new`, `/post/new`, `/post/<id>/edit`), moderação em massa e individual (`/forum/<id>/edit`, `/topic/<id>/lock`, `/topic/<id>/hide`, etc.), busca (`/search`), listagem de membros (`/memberlist`) e o rastreador de tópicos (`/topictracker`).
- **Hook de plugin `flaskbb_load_blueprints`** (`views.py:1171`): é assim, e não por importação direta, que o módulo se registra na aplicação, o mesmo mecanismo de extensão que plugins de terceiros usam.
- **`forum.before_request(force_login_if_needed)`** (`views.py:1333`, implementado em `utils.py`): roda antes de toda requisição do blueprint, verificando se o fórum acessado exige login.

## Destinos de saída

- **Modelos persistidos** (via SQLAlchemy, commitados no banco): `Category`, `Forum`, `Topic`, `Post`, `TopicsRead`, `ForumsRead`, `Report`.
- **Eventos de plugin disparados** (`pluggy.hook.*`, em `forms.py` e `models.py`): `flaskbb_form_post_save`, `flaskbb_form_topic_save`, `flaskbb_event_post_save_before/after`, `flaskbb_event_topic_save_before/after`. O módulo **não envia e-mail nem notificação diretamente**, quem faz isso são plugins/outros módulos que escutam esses eventos (ex.: um plugin de notificação por e-mail reagiria a `flaskbb_event_post_save_after` para avisar quem está rastreando o tópico). Isso é relevante pra Parte 3.2: acoplamento entre `forum/` e comportamentos de notificação é indireto, via o barramento de eventos do `pluggy`, não uma dependência direta de código.
- **Índice de busca Whoosh** (via `flask_whooshee`, chamado a partir de `forms.py::SearchPageForm`/`UserSearchForm`): `Topic`, `Post`, `Forum` e `User` são indexados para busca full-text.

## Docstrings novas

Adicionadas no fork (commit `docs(forum): adiciona docstrings em Post.hide/unhide, current_* proxies e _get_item`):

1. **`Post.hide()`**, `flaskbb/forum/models.py:359`. Não óbvio: se o post é o primeiro do tópico, `hide()` na verdade oculta o **tópico inteiro** (delega para `self.topic.hide(user)`), não só aquele post. Quem lê só a assinatura (`hide(self, user)`) não tem como adivinhar isso.
2. **`Post.unhide()`**, `flaskbb/forum/models.py:386`. Espelha o mesmo comportamento não óbvio de `hide()`.
3. **`current_topic()`**, `flaskbb/forum/locals.py:31` (e as outras 3 proxies `current_post`/`current_forum`/`current_category` ao lado). Documenta a cascata de resolução (`current_topic` cai pra trás em `current_post.topic` antes de olhar `topic_id` na URL), que não é óbvia a partir do nome nem do corpo de 3 linhas.
4. **`_get_item()`**, `flaskbb/forum/locals.py:62`. Documenta o mecanismo de memoização em `flask.g` por trás das 4 proxies acima, inclusive um aviso prático: esse cache pode vazar entre testes que reaproveitam o mesmo contexto de aplicação (o que já nos mordeu de verdade nos testes da Parte 1, ver `tests/unit/forum/test_forum_locals.py`).