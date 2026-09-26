# Retrospectiva — Síntese das 3 Partes

## O que ficou objetivamente melhor

**Cobertura de testes.** No `flaskbb/forum/`, saiu de 33% (baseline real da Tarefa 1.1, depois de corrigir o problema de medição do `pytest-xdist`) para 66% ao fim da Parte 1 e 71% ao fim da Parte 2 — com `forms.py` indo de 0% para 73% e `utils.py`/`locals.py` batendo 90%+. Isso não é só um número: são 15 testes novos (Partes 1 e 2 somadas) cobrindo justamente os pontos que estavam sem nenhuma rede de segurança — `ManageForum.post()`, o método mais complexo do arquivo, tinha 0% de cobertura até a Parte 2.

**Dois bugs reais encontrados e corrigidos**, não hipotéticos:
- `UnhidePost.post()` tinha um `redirect(...)` sem `return` — desocultar um post já visível reprocessava a ação e mostrava duas mensagens de flash contraditórias. Só apareceu porque catalogar os *code smells* da Parte 2 me fez ler as 4 classes de hide/unhide lado a lado, e a assimetria saltou aos olhos.
- `Forum.move_topics_to()` tem uma docstring que promete "retorna `True` se todos os tópicos foram movidos", mas o código só reflete o resultado do último tópico do laço — achado na Parte 3, ao reler o método com a pergunta "o que esse código promete versus o que ele realmente faz?".

Nenhum dos dois apareceria só rodando a suíte de testes original (233 testes passando, nenhum tocando esses caminhos) — os dois só vieram à tona porque a disciplina pediu pra *ler* o código com atenção, não só executá-lo.

**Legibilidade mensurável.** `ManageForum.post()` caiu de 126 para menos de 50 linhas, sem o `# noqa: C901`/`# TODO(anr): Clean this up. @_@` que — como a Parte 3 mostrou puxando o histórico real do Git — estava lá reconhecido como dívida desde 19 de maio de 2018, quase 8 anos parado. 4 classes de moderação quase idênticas (`LockTopic`/`UnlockTopic`/`HighlightTopic`/`TrivializeTopic`) viraram uma função compartilhada; 19 chamadas com um `True` posicional sem significado óbvio viraram argumentos nomeados.

**Documentação onde faltava.** 4 docstrings novas em pontos que a assinatura sozinha não explica — o melhor exemplo é `Post.hide()`, que, se o post for o primeiro do tópico, silenciosamente oculta o tópico inteiro em vez de só o post. Ninguém que só lesse `def hide(self, user):` adivinharia isso.

## Técnicas mais úteis vs. mais difíceis de aplicar

**Mais útil: teste com dublê pra provar interação, não só resultado** (Tarefa 1.5). Mockar `time_utcnow()` em `Post.save()` revelou que o relógio é lido *duas vezes* por post novo (uma em `__init__`, outra em `save()`, que sobrescreve a primeira) — uma redundância real que um teste checando só o valor final de `date_created` jamais capturaria, porque as duas chamadas retornam o mesmo timestamp na prática. Foi a técnica que mais me ensinou algo sobre o código que eu não teria descoberto só lendo.

**Mais útil, segunda colocada: Extract Method aplicado a duplicação real** (Tarefa 2.3). Não é só estética — a duplicação entre `HidePost`/`UnhidePost` era literalmente a causa do bug do `return` ausente. Depois da extração, ficou estruturalmente impossível uma das 4 classes "esquecer" o guard clause, porque só existe um lugar onde ele é escrito.

**Mais difícil: medir cobertura de forma confiável.** Boa parte do tempo da Parte 1 (e um bocado da Parte 2) foi gasto não escrevendo testes, mas descobrindo que o `pytest-xdist` padrão do projeto sub-relata cobertura (`forms.py` aparecia com 22% quando o real era 73%) — e que rodar sem paralelismo pra contornar isso expõe 11 falhas intermitentes não relacionadas (incompatibilidade entre a lib `responses` e `requests` em Python 3.14). Nenhuma disciplina "ensina" isso diretamente; foi ferramental do mundo real atravessando o exercício acadêmico, e exigiu validar tudo duas vezes (com e sem paralelismo) pra ter confiança nos números reportados.

**Também difícil: refatorar sem mudar comportamento observável em código com pouca cobertura prévia.** A refatoração de `ManageForum.post()` (a de maior risco do plano) só ficou segura depois de escrever 9 testes cobrindo as 8 ações *antes* de considerar o trabalho terminado — sem eles, eu não teria como garantir que a tabela de despacho (`_BULK_ACTIONS`) mapeava cada ação pro campo e `reverse` certos. Refatorar sem essa rede de segurança teria sido apostar, não engenharia.

## O que eu faria diferente

Escreveria os testes da Parte 1 **por arquivo, não por tarefa**. Fui seguindo a ordem do enunciado (1.3 → 1.4 → 1.5), o que me fez voltar a `forms.py` e `views.py` várias vezes em momentos diferentes. Se eu tivesse decidido desde o início "vou fechar `forms.py` inteiro, depois `locals.py` inteiro, depois `views.py`", teria escrito menos código de setup repetido entre os arquivos de teste.

Também teria medido a cobertura sem paralelismo desde a Tarefa 1.1, em vez de só descobrir o problema do `pytest-xdist` na Tarefa 1.6. Isso teria evitado ter que documentar retroativamente que a baseline original provavelmente estava subestimada, e as metas da Tarefa 1.2 teriam sido calculadas em cima de números confiáveis desde o começo.

Por fim, teria catalogado os *code smells* da Parte 2 antes de terminar a Parte 1, não depois — vários dos cenários de teste que escrevi na Tarefa 1.3 (por exemplo, os casos de borda de `HidePost`/`UnhidePost`) teriam sido ainda mais direcionados se eu já soubesse, desde aquele momento, que aquele trecho específico concentrava duplicação de código — o que só ficou claro quando catalogamos os smells na Parte 2.