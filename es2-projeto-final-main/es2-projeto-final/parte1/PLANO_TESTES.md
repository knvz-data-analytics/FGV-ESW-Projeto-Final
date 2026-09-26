### Tarefa 1.2 — Escolha do módulo-alvo e meta de cobertura (1,0 ponto)

Escolha **um único módulo** entre `flaskbb/forum/`, `flaskbb/management/` e `flaskbb/user/` para ser o alvo das tarefas seguintes.

Em `parte1/PLANO_TESTES.md`, registre:

- Módulo escolhido e justificativa breve (1–2 parágrafos).
- Cobertura atual do módulo (linhas/branches).
- Meta concreta de incremento (ex.: "+15 pontos percentuais de cobertura de linhas" ou "cobrir todas as funções de `forum/views.py` com mais de 5 ramos").
- Lista inicial de **cenários ainda não cobertos** que você pretende atacar (mínimo 6 itens, marcando quais são caminhos felizes e quais são bordas/erros).

**Entrega:** arquivo `parte1/PLANO_TESTES.md` neste repositório.

Perfeito, agora dá pra fechar os números reais. Aqui está a versão final da seção de cobertura + meta para o `parte1/PLANO_TESTES.md`:

### Cobertura atual do módulo (baseline real)

| Arquivo | Stmts | Miss | Cover |
|---|---|---|---|
| `flaskbb/forum/__init__.py` | 2 | 2 | 0% |
| `flaskbb/forum/forms.py` | 91 | 91 | **0%** |
| `flaskbb/forum/locals.py` | 29 | 18 | 38% |
| `flaskbb/forum/models.py` | 641 | 271 | 58% |
| `flaskbb/forum/utils.py` | 10 | 7 | 30% |
| `flaskbb/forum/views.py` | 499 | 465 | **7%** |
| **TOTAL** | **1272** | **854** | **33%** |

Dois pontos são nitidos a não cobertura: `forms.py` está em **0%** (nenhuma linha praticada, mesmo tendo a lógica de `save()` não trivial em `PostForm`, `ReplyForm`, `TopicForm`, `EditTopicForm`) e `views.py` está em só **7%**, com quase tudo centralizado em `ManageForum.post` (o método com 8 ramos de ação) sem cobertura nenhuma.

### Meta concreta de incremento

- **Cobertura de linhas do módulo: de 33% para pelo menos 48% (+15 pontos percentuais)** — dá pra alcançar isso mesmo sem cobrir tudo de `views.py`, priorizando:
- **`forum/forms.py`: sair de 0% para ≥ 70%** cobrindo os métodos `save()` de `PostForm`, `ReplyForm`, `TopicForm` e `EditTopicForm`, que são lógica de negócio pura e de certa forma tranquilso de isolar.
- **`forum/views.py`: sair de 7% para ≥ 20%**, focando nos 8 ramos de `ManageForum.post` (lock/unlock/highlight/trivialize/delete/move/hide/unhide) e nos casos de borda já listados (cenários 1–8 da tabela anterior).
- **`forum/utils.py` e `forum/locals.py`: chegar a 100%** — são arquivos pequenos e o candidato natural ao teste com mock.