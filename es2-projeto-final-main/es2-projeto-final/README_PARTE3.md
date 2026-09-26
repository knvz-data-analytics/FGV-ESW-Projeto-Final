# Projeto Final ES2 — Parte 3

A Parte 3 trabalha sobre o módulo escolhido na Parte 1 (e refinado na
Parte 2). Foco em **compreender o código como sistema legado**,
**documentar** decisões de design e **planejar** evolução. Esta parte
**não exige implementação** da proposta de evolução — somente
descrição em profundidade.

Escopo de atuação permanece em `flaskbb/forum/`, `flaskbb/management/`
ou `flaskbb/user/`.

---

## Tarefas da Parte 3

### Tarefa 3.1 — Documentação estratégica do módulo (2,0 pontos)

Crie `parte3/MODULO.md`, um README do módulo escolhido,
contendo:

- Propósito do módulo em 1 parágrafo (papel no domínio do fórum).
- Mapa dos arquivos principais (tabela com nome → responsabilidade
  em 1 linha).
- Pontos de entrada externos (rotas, comandos CLI, eventos) e
  destinos de saída (modelos persistidos, e-mails, etc.).
- **Pelo menos 3 docstrings novas ou reescritas** em pontos não
  óbvios do módulo (funções/classes onde a leitura do nome não basta
  para entender o contrato). Citar arquivos e linhas em
  `MODULO.md`.

**Entrega:** arquivo `parte3/MODULO.md` neste repositório +
commits com as docstrings no fork de flaskbb.

---

### Tarefa 3.2 — Análise como sistema legado (3,0 pontos)

Em `parte3/ANALISE_LEGADO.md`, analise o módulo
escolhido sob a perspectiva de manutenção e evolução:

- **Dependências internas e externas:** quem o módulo importa, quem
  o importa. Diagrama em Mermaid (`graph LR`) mostrando o módulo no
  centro e suas conexões diretas.
- **Acoplamento e coesão:** indicar 2 pontos de alto acoplamento e
  1 ponto de baixa coesão, com justificativa.
- **Pontos de fragilidade:** liste 3 trechos que você considera
  arriscados para alterar (ex.: uso intenso de estado global, lógica
  duplicada em vários arquivos, falta de testes em caminho crítico).
- **Aplicação da Lei de Lehman:** discuta, em 1 parágrafo, qual(is)
  das leis de Lehman você observa atuando neste módulo e com base
  em que evidência (idade do código, histórico de commits, tamanho
  dos arquivos, etc.).
- **Seams identificáveis:** indique 2 pontos do módulo onde seria
  natural introduzir um *seam* para permitir substituição de
  comportamento em teste ou em produção.

**Entrega:** arquivo `parte3/ANALISE_LEGADO.md` (com
diagrama Mermaid inline).

---

### Tarefa 3.3 — Proposta de evolução (4,0 pontos)

Escolha **uma única** proposta de evolução para o módulo, entre as
opções abaixo (ou outra equivalente, combinada com o professor):

- Extrair o módulo `forum/` (ou parte dele) como um **serviço
  separado** com API HTTP, mantendo o restante do flaskbb como
  cliente.
- Substituir a **camada de persistência** atual (SQLAlchemy
  direto nos modelos) por uma camada de repositórios, isolando o
  ORM.
- Aposentar progressivamente uma parte do código antigo aplicando
  **Branch by Abstraction** ou **execução paralela** sobre um
  comportamento real (ex.: paginação, busca, notificação).
- Propor a migração do módulo escolhido para outra stack/linguagem
  (justificando trade-offs), com plano de transição incremental.

Em `parte3/PROPOSTA_EVOLUCAO.md`, descreva:

- **Motivação** (1–2 parágrafos): qual problema do estado atual a
  proposta resolve, conectando explicitamente a achados das
  Tarefas 3.1 e 3.2.
- **Estado-alvo** descrito com:
  - Diagrama Mermaid de componentes / fluxos do estado-alvo.
  - Lista de novos artefatos (módulos, serviços, contratos).
- **Plano de migração incremental** com pelo menos **5 passos**
  numerados, cada um descrevendo:
  - O que muda no código.
  - Como o sistema continua funcionando enquanto o passo está em
    andamento (sem big bang).
  - Como a estratégia é testada / verificada.
- **Riscos e mitigações:** 3 riscos relevantes, cada um com
  estratégia de mitigação.
- **O que fica fora do escopo** da proposta (limites claros).

A proposta é **descritiva** — não é necessário implementar nada
nesta parte. A qualidade do plano (clareza, viabilidade,
conexão com a análise) é o que será avaliado.

**Entrega:** arquivo `parte3/PROPOSTA_EVOLUCAO.md`.

---

### Tarefa 3.4 — Síntese das 3 partes (1,0 ponto)

Em `parte3/RETROSPECTIVA.md`, escreva uma síntese
curta (1–2 páginas) cobrindo:

- O que ficou objetivamente melhor no módulo entre o estado inicial
  (Parte 1) e o estado final (Parte 3).
- Quais técnicas da disciplina foram mais úteis e quais foram mais
  difíceis de aplicar neste sistema, com exemplo concreto.
- O que você faria diferente se recomeçasse o projeto hoje.

**Entrega:** arquivo `parte3/RETROSPECTIVA.md`.

---

## Pontuação Total da Parte 3: 10,0 pontos

| Tarefa | Pontos |
|---|---|
| 3.1 — Documentação estratégica do módulo | 2,0 |
| 3.2 — Análise como sistema legado | 3,0 |
| 3.3 — Proposta de evolução | 4,0 |
| 3.4 — Retrospectiva | 1,0 |
| **Total** | **10,0** |

---

## Forma de Entrega

- **Documentos** (`MODULO.md`, `ANALISE_LEGADO.md`,
  `PROPOSTA_EVOLUCAO.md`, `RETROSPECTIVA.md`) neste repositório do
  Classroom, na pasta `parte3/`.
- **Commits com as docstrings novas/reescritas** (Tarefa 3.1) no seu
  fork de `jeffsantos/flaskbb`.
- Diagramas obrigatoriamente em Mermaid embutido nos `.md`.
- Submeter no D2L:
  - Link deste repositório + hash do commit final da Parte 3.
  - Link do fork de `jeffsantos/flaskbb` + hash do commit das
    docstrings.

## Prazo

Até o fim da **semana 9**.
