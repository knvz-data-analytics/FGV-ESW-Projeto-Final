# Projeto Final — Engenharia de Software II

## Visão Geral

O Projeto Final de Engenharia de Software II é uma atividade contínua
ao longo das 10 semanas da disciplina (sendo a semana 10 reservada à
prova final), dividida em **3 partes progressivas**. Cada parte vale
**10,0 pontos**, totalizando **30,0 pontos** no projeto inteiro.

O projeto consiste em atuar como **mantenedor e evoluidor** de um
sistema de fórum real em Python/Flask: o
[`flaskbb`](https://github.com/flaskbb/flaskbb). O aluno (individual
ou em dupla) trabalha sobre um fork didático mantido pelo professor
em `https://github.com/jeffsantos/flaskbb`, aplicando ao longo das
3 partes as técnicas estudadas na disciplina: testes automatizados,
refactoring, identificação de code smells, melhoria de legibilidade,
compreensão de sistemas legados e planejamento de evolução.

A disciplina anterior (Engenharia de Software I) usou o
`esmforum/esmforum-react` como sistema-base. ES2 troca para o
`flaskbb` por consistência com a stack Python usada nos exercícios
práticos e nas práticas avaliadas, e porque o `flaskbb` oferece um
hotspot rico e realista para o tema central de ES2 — qualidade
interna do código e sustentabilidade de sistemas ao longo do tempo.

## Por que `flaskbb`

- **Domínio análogo ao esmforum** (categorias → fóruns → tópicos →
  posts), preservando o espírito do projeto final de ES1.
- **Stack Python**, consistente com as Práticas Avaliadas, os
  exercícios práticos e o panorama de linguagens da disciplina.
- **Tamanho realista** (~24 kLOC) com suíte de testes existente
  robusta, ideal para exercícios de cobertura, refactoring e
  análise de código legado.
- **Hotspots claros** para refactoring nos módulos de domínio
  (`flaskbb/forum/`, `flaskbb/management/`, `flaskbb/user/`).

## Divisão em Partes

| Parte | Semanas | Unidades cobertas | Tema | Pontos |
|---|---|---|---|---|
| Parte 1 | 1 a 3 | U1, U2 | Testes (Básico e Avançado) | 10,0 |
| Parte 2 | 4 a 6 | U3, U4, U6 | Refactoring, Code Smells e Código Legível | 10,0 |
| Parte 3 | 7 a 9 | U5, U7, U8, U9 | Compreensão, Manutenção, Evolução, Sistemas Legados | 10,0 |
| **Total** | | | | **30,0** |

Cada parte tem seu enunciado próprio, publicado no **template do
repositório de entrega** (`es2-projeto-final/`), que é a base do
assignment do GitHub Classroom:

- [`es2-projeto-final/README_PARTE1.md`](es2-projeto-final/README_PARTE1.md)
  — apresentação do flaskbb e tarefas da Parte 1 (até a semana 3).
- [`es2-projeto-final/README_PARTE2.md`](es2-projeto-final/README_PARTE2.md)
  — tarefas da Parte 2 (até a semana 6).
- [`es2-projeto-final/README_PARTE3.md`](es2-projeto-final/README_PARTE3.md)
  — tarefas da Parte 3 (até a semana 9).

As **rubricas detalhadas de avaliação** e o mapeamento
tarefa ↔ unidade ↔ conceito estão em
[`orientacoes_professor.md`](orientacoes_professor.md), de uso do
professor/tutor.

O **guia de configuração do ambiente** (fork, instalação, baseline
de testes, versão de referência) está em
[`es2-projeto-final/guia-setup-ambiente.md`](es2-projeto-final/guia-setup-ambiente.md),
dentro do template do repositório.

## Regras Gerais

- **Formato:** individual ou em duplas. Não há diferença de exigência
  ou pontuação entre as duas modalidades.
- **Repositórios de trabalho:** o aluno (ou dupla) usa **dois**
  repositórios:
  - **Repositório do Classroom**, criado a partir do template em
    `material/8-Projeto-Final/es2-projeto-final/` (link do
    assignment fornecido pelo professor). Recebe os **documentos de
    análise** (`.md`) em `parte1/`, `parte2/`, `parte3/`.
  - **Fork de `https://github.com/jeffsantos/flaskbb`** (e não do
    upstream `flaskbb/flaskbb`). Recebe os **commits de código**
    (testes novos, refatorações, docstrings). O fork do professor
    congela versão e ajustes didáticos.
- **Entrega:** submissão no D2L com **link + hash** do repositório
  do Classroom (commit final da parte) e **link + hash** do fork de
  flaskbb (commit correspondente), conforme prazos abaixo.
- **Prazos:**
  - Parte 1 — até o fim da semana 3.
  - Parte 2 — até o fim da semana 6.
  - Parte 3 — até o fim da semana 9.
- **Notação gráfica:** todos os diagramas devem ser feitos em
  **Mermaid** dentro de markdown, garantindo renderização direta no
  GitHub e no D2L.
- **Critérios de autoria / uso de IA:** ver
  [`orientacoes_professor.md`](orientacoes_professor.md). Em
  qualquer caso, o aluno é responsável por compreender e ser capaz
  de justificar oralmente cada decisão técnica entregue.

## Relação com outros entregáveis

- As **Práticas Avaliadas** ([material/7-Praticas-Avaliadas/](../7-Praticas-Avaliadas/))
  e os **Exercícios Práticos** ([material/5-Exercicios/](../5-Exercicios/))
  preparam tecnicamente o aluno para cada parte do projeto.
- O **Plano de Estudos** de cada unidade ([material/2-Plano-de-Estudos/](../2-Plano-de-Estudos/))
  indica capítulos dos livros-texto e materiais complementares.
- A **prova final** (semana 10) não é coberta por este entregável.
