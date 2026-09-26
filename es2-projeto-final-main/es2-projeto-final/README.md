# Projeto Final — Engenharia de Software II

Este repositório é o **workspace de entrega** do Projeto Final de
Engenharia de Software II. Ele é criado a partir do template
publicado pelo professor via GitHub Classroom e recebe os
**documentos de análise** produzidos ao longo das 3 partes do
projeto.

## Estrutura

- [`README_PARTE1.md`](README_PARTE1.md) — tarefas da Parte 1
  (Testes; semanas 1–3).
- [`README_PARTE2.md`](README_PARTE2.md) — tarefas da Parte 2
  (Refactoring, Code Smells, Legibilidade; semanas 4–6).
- [`README_PARTE3.md`](README_PARTE3.md) — tarefas da Parte 3
  (Compreensão, Legado, Evolução; semanas 7–9).
- [`guia-setup-ambiente.md`](guia-setup-ambiente.md) — setup do
  ambiente e do fork do flaskbb.
- `parte1/`, `parte2/`, `parte3/` — pastas onde vão os documentos
  entregues em cada parte.

## Dois repositórios de trabalho

O projeto usa **dois repositórios**:

1. **Este repositório (Classroom)** recebe os documentos `.md` de
   análise (baseline, plano de testes, catálogo de smells, análise
   de legado, proposta de evolução etc.) em `parte1/`, `parte2/`,
   `parte3/`.
2. **Seu fork de [`jeffsantos/flaskbb`](https://github.com/jeffsantos/flaskbb)**
   recebe os **commits de código** (novos testes, refatorações,
   docstrings). Instruções completas no
   [`guia-setup-ambiente.md`](guia-setup-ambiente.md).

Cada entrega no D2L inclui **link + hash** dos dois repositórios.

## Ambiente

O `.devcontainer/` deste repositório provê Python 3.11 para o
workspace (útil se você quiser rodar snippets ou validar
sintaxe). O ambiente de execução do flaskbb (com `uv`, banco,
etc.) é configurado **dentro do fork de flaskbb**, conforme o
[`guia-setup-ambiente.md`](guia-setup-ambiente.md).
