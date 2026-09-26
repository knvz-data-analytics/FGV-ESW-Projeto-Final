# Projeto Final ES2 — Parte 1

## Contexto

O `flaskbb` é um software de fórum em Python/Flask, organizado em
categorias → fóruns → tópicos → posts, com painel de administração,
sistema de permissões por grupo e suíte de testes existente. O
código vive em https://github.com/flaskbb/flaskbb. Para a disciplina,
você trabalha sobre o fork didático
`https://github.com/jeffsantos/flaskbb`.

Seu papel nas 3 partes do projeto é o de um **mantenedor que acaba
de assumir o sistema**: você precisa entender o código existente,
aumentar sua confiança via testes, melhorar sua estrutura e
documentar o caminho de evolução. Esta primeira parte foca em
**conhecer o sistema e fortalecer sua suíte de testes**.

Antes de começar, siga o
[`guia-setup-ambiente.md`](guia-setup-ambiente.md) e confirme que a
baseline de testes está 100% verde.

---

## Tarefas da Parte 1

### Tarefa 1.1 — Setup do ambiente e baseline de testes (2,0 pontos)

- Fork de `jeffsantos/flaskbb` (individual ou da dupla), clone local
  e configuração do `uv`/virtualenv conforme o guia.
- Execução de `uv run pytest` com 100% de sucesso, evidenciada por
  um print/saída salvo em `parte1/BASELINE.md`.
- Geração de relatório de cobertura por módulo para
  `flaskbb/forum/`, `flaskbb/management/` e `flaskbb/user/`,
  registrando os valores iniciais no mesmo arquivo.

**Entrega:** arquivo `parte1/BASELINE.md` neste repositório
(inclua no arquivo o link do seu fork de flaskbb).

---

### Tarefa 1.2 — Escolha do módulo-alvo e meta de cobertura (1,0 ponto)

Escolha **um único módulo** entre `flaskbb/forum/`, `flaskbb/management/`
e `flaskbb/user/` para ser o alvo das tarefas seguintes.

Em `parte1/PLANO_TESTES.md`, registre:

- Módulo escolhido e justificativa breve (1–2 parágrafos).
- Cobertura atual do módulo (linhas/branches).
- Meta concreta de incremento (ex.: "+15 pontos percentuais de
  cobertura de linhas" ou "cobrir todas as funções de
  `forum/views.py` com mais de 5 ramos").
- Lista inicial de **cenários ainda não cobertos** que você
  pretende atacar (mínimo 6 itens, marcando quais são caminhos
  felizes e quais são bordas/erros).

**Entrega:** arquivo `parte1/PLANO_TESTES.md` neste repositório.

---

### Tarefa 1.3 — Novos testes unitários: caminhos felizes e bordas (3,0 pontos)

Acrescente, no diretório de testes do módulo escolhido, **pelo
menos 8 casos novos**, distribuídos entre:

- Caminhos felizes do fluxo principal do módulo.
- Casos de borda: entradas vazias, limites de tamanho/permissão,
  ordenação, paginação, etc.
- Erros esperados (validações, exceções de negócio).

Requisitos:

- Cada caso deve ter nome descritivo e *uma asserção principal* clara.
- Os testes devem rodar isolados (sem depender de ordem) e em
  conjunto com `uv run pytest`.
- Não desabilitar nem alterar testes existentes.

**Entrega:** commits no fork de flaskbb + lista de casos em
`parte1/NOVOS_TESTES.md` (neste repositório) apontando, para cada
caso, arquivo/linha e tipo (feliz, borda, erro).

---

### Tarefa 1.4 — Testes parametrizados (1,5 pontos)

Inclua **pelo menos 1 teste parametrizado** (usando
`pytest.mark.parametrize`) cobrindo ao menos 4 combinações de
entrada relevantes do módulo escolhido (ex.: validação de slug de
fórum, regras de visibilidade de tópico, contagem de tópicos não
lidos por usuário).

Requisito de qualidade: as combinações devem incluir tanto entradas
válidas quanto inválidas.

**Entrega:** commit do teste no fork de flaskbb + referência ao
arquivo em `parte1/NOVOS_TESTES.md` (neste repositório).

---

### Tarefa 1.5 — Testes com dublê / mock (1,5 pontos)

Inclua **pelo menos 1 teste** que utilize um objeto dublê (mock,
stub ou fake) para isolar uma unidade de uma dependência. Use
`unittest.mock` ou utilitários equivalentes.

Exemplos de alvos no flaskbb:

- Substituir o envio de e-mail (`flaskbb/email.py`) para testar
  fluxo de notificação de usuário.
- Substituir o relógio (`datetime`) para testar regras dependentes
  de tempo.
- Substituir uma função de cache para verificar invalidação.

O teste deve verificar **interação** com o dublê (ex.: chamado uma
vez, com determinado argumento), não apenas o resultado final.

> **Nota sobre escopo:** o teste deve ficar no diretório do módulo
> de domínio escolhido (`forum/`, `management/` ou `user/`). O dublê
> apenas isola uma dependência externa para tornar o teste viável —
> o foco continua sendo o comportamento do módulo sob teste, não a
> dependência mockada.

**Entrega:** commit do teste no fork de flaskbb + referência em
`parte1/NOVOS_TESTES.md` (neste repositório) explicando, em 2–3
frases, por que o dublê foi necessário.

---

### Tarefa 1.6 — Relatório de cobertura final e cenários remanescentes (1,0 ponto)

Ao final da Parte 1:

- Gere novo relatório de cobertura para o módulo-alvo.
- Compare com a baseline da Tarefa 1.1 e com a meta da Tarefa 1.2.
- Liste, em `parte1/COBERTURA_FINAL.md`, os cenários
  que **continuam descobertos** e uma sugestão de como cobri-los
  futuramente (sem precisar implementar).

**Entrega:** arquivo `parte1/COBERTURA_FINAL.md` neste
repositório.

---

## Pontuação Total da Parte 1: 10,0 pontos

| Tarefa | Pontos |
|---|---|
| 1.1 — Setup e baseline | 2,0 |
| 1.2 — Escolha de módulo e meta de cobertura | 1,0 |
| 1.3 — Novos testes (felizes/bordas/erros) | 3,0 |
| 1.4 — Teste parametrizado | 1,5 |
| 1.5 — Teste com dublê/mock | 1,5 |
| 1.6 — Relatório final de cobertura | 1,0 |
| **Total** | **10,0** |

---

## Forma de Entrega

- **Documentos** (`BASELINE.md`, `PLANO_TESTES.md`, `NOVOS_TESTES.md`,
  `COBERTURA_FINAL.md`) neste repositório do Classroom, na pasta
  `parte1/`.
- **Testes novos** no diretório de testes correspondente ao módulo
  escolhido, no **seu fork de `jeffsantos/flaskbb`**.
- Submeter no D2L:
  - Link deste repositório + hash do commit final da Parte 1.
  - Link do fork de `jeffsantos/flaskbb` + hash do commit dos testes.

## Prazo

Até o fim da **semana 3**.
