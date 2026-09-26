# Projeto Final ES2 — Parte 2

A Parte 2 trabalha sobre o mesmo fork do flaskbb da Parte 1 e cobre
os temas das Unidades **U3 (Refactoring)**, **U4 (Refactoring: Code
Smells)** e **U6 (Código Legível)**. Toda a suíte de testes existente
**e os novos testes da Parte 1** devem continuar verdes ao final.

Sugestão de hotspots para refatorar (escolha um como alvo principal):

- `flaskbb/forum/models.py` (~1665 LOCs)
- `flaskbb/forum/views.py` (~1327 LOCs)
- `flaskbb/management/views.py` (~1499 LOCs)

O escopo das tarefas deve permanecer em `flaskbb/forum/`,
`flaskbb/management/` ou `flaskbb/user/`.

---

## Tarefas da Parte 2

### Tarefa 2.1 — Catálogo de code smells (2,0 pontos)

Escolha um arquivo de hotspot dentro do escopo permitido e produza
um catálogo, em `parte2/CODE_SMELLS.md`, com **pelo
menos 6 smells distintos** encontrados. Para cada smell:

- Nome do smell (ex.: Long Method, Long Parameter List, Feature Envy,
  Duplicated Code, Primitive Obsession, Data Clumps, Switch
  Statements, Comentários redundantes, etc.).
- Localização exata (`arquivo:linhas`).
- Trecho de código (citado em fence ```python).
- Explicação curta de **por que** é um smell naquele contexto.

**Entrega:** arquivo `parte2/CODE_SMELLS.md`.

---

### Tarefa 2.2 — Plano de refactoring (1,0 ponto)

Em `parte2/PLANO_REFACTORING.md`, escolha **4 smells
do catálogo** para tratar nesta parte e proponha, para cada um:

- Refatoração nomeada do catálogo de Fowler (ex.: Extract Method,
  Extract Variable, Inline Method, Move Method, Extract Class,
  Rename, Replace Conditional with Polymorphism, Replace Magic
  Number with Symbolic Constant).
- Resultado esperado em uma frase.
- Riscos antecipados (ex.: efeito colateral em outro módulo).

**Entrega:** arquivo `parte2/PLANO_REFACTORING.md`.

---

### Tarefa 2.3 — Aplicação das refatorações (4,0 pontos)

Aplique as 4 refatorações do plano em commits separados, cada um
contendo:

- Mensagem de commit no formato
  `refactor(<escopo>): <transformação aplicada>` (ex.:
  `refactor(forum): extract method de save_topic`).
- Mudança pequena e focada — uma refatoração por commit.
- Toda a suíte (`uv run pytest`) verde após o commit.

Requisitos adicionais:

- **Pelo menos uma** das 4 deve ser uma Extract Method ou Extract
  Class aplicada a um dos métodos longos do hotspot.
- **Pelo menos uma** deve eliminar duplicação de código real.
- Nenhuma refatoração deve alterar o comportamento observável
  testado pela suíte.

**Entrega:** sequência de 4 commits no fork de flaskbb + arquivo
`parte2/REFATORACOES.md` (neste repositório) listando, para cada
commit: hash, smell tratado, transformação aplicada e antes/depois
resumido (pode citar trechos curtos).

---

### Tarefa 2.4 — Melhorias de legibilidade (2,0 pontos)

Atue sobre o mesmo arquivo (ou um arquivo vizinho dentro do escopo)
aplicando **pelo menos 3 melhorias de legibilidade** distintas,
cada uma de uma categoria diferente entre:

- Nomenclatura (renomear variável/função/parâmetro para nome mais
  expressivo, alinhado à linguagem ubíqua do domínio do fórum).
- Estilo de código (formatação, ordem dos imports, quebra de
  expressões compostas).
- Tratamento de exceções (capturar exceção mais específica,
  reescrever bloco `try/except` ruidoso, transformar retorno de
  código em exceção quando apropriado).
- Substituição de comentário redundante por código
  autoexplicativo, ou eliminação de comentário desatualizado.

Cada melhoria deve estar em commit próprio com mensagem
`refactor(<escopo>): <melhoria>` e ser registrada em
`parte2/LEGIBILIDADE.md` com antes/depois e
justificativa em 1–2 frases.

**Entrega:** commits no fork de flaskbb + arquivo
`parte2/LEGIBILIDADE.md` (neste repositório).

---

### Tarefa 2.5 — Validação final (1,0 ponto)

- Rode novamente a suíte completa (`uv run pytest`) e o relatório
  de cobertura do módulo trabalhado na Parte 1.
- Verifique se a cobertura **não regrediu** em relação ao final da
  Parte 1.
- Em `parte2/VALIDACAO.md`, registre saída do `pytest`,
  cobertura antes/depois desta parte e um parágrafo curto sobre o
  que mudou na sua leitura do código depois das refatorações.

**Entrega:** arquivo `parte2/VALIDACAO.md`.

---

## Pontuação Total da Parte 2: 10,0 pontos

| Tarefa | Pontos |
|---|---|
| 2.1 — Catálogo de code smells | 2,0 |
| 2.2 — Plano de refactoring | 1,0 |
| 2.3 — Aplicação das refatorações | 4,0 |
| 2.4 — Melhorias de legibilidade | 2,0 |
| 2.5 — Validação final | 1,0 |
| **Total** | **10,0** |

---

## Forma de Entrega

- **Documentos** (`CODE_SMELLS.md`, `PLANO_REFACTORING.md`,
  `REFATORACOES.md`, `LEGIBILIDADE.md`, `VALIDACAO.md`) neste
  repositório do Classroom, na pasta `parte2/`.
- **Commits de refatoração e legibilidade** no seu fork de
  `jeffsantos/flaskbb` (em branch própria `parte2` ou diretamente em
  `main` do fork — combinar internamente na dupla).
- Submeter no D2L:
  - Link deste repositório + hash do commit final da Parte 2.
  - Link do fork de `jeffsantos/flaskbb` + hash do commit final da
    refatoração.

## Prazo

Até o fim da **semana 6**.
