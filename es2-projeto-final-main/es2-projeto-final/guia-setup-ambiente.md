# Guia de Configuração do Ambiente — Projeto Final ES2

O sistema-base do projeto final é o
[`flaskbb`](https://github.com/flaskbb/flaskbb), um software de fórum
em Python/Flask. O projeto da disciplina **não** parte do upstream
diretamente: parte de um **fork didático mantido pelo professor** em
`https://github.com/jeffsantos/flaskbb`. Esse fork congela a versão
de referência e pode receber pequenos ajustes didáticos.

## 1. Versão de referência

A versão usada como baseline para esta turma é o commit
`22989b72419d4083989cc6123be69228607ac7b1` do upstream
`flaskbb/flaskbb` ("a couple of fixes around redis cacheing"). O fork
`jeffsantos/flaskbb` é alinhado a esse commit no início do trimestre.

Qualquer atualização do fork durante o trimestre será comunicada na
disciplina. Não atualize seu fork a partir do upstream sem combinar
com o professor.

## 2. Pré-requisitos

- **Python 3.10 ou superior** (recomendado: 3.11).
- **`uv`** (gerenciador de ambiente/pacotes adotado pelo projeto).
  Instalação: ver https://docs.astral.sh/uv/getting-started/installation/
- **Git**.
- **Make** (opcional, mas conveniente — os comandos do `Makefile`
  ficam mais curtos). No Windows, funciona via Git Bash ou WSL.
- Em sistemas Linux, pode ser necessário instalar pacotes de
  desenvolvimento (`build-essential`, `libffi-dev`, `libssl-dev`).

## 3. Fork e clone

1. Acesse `https://github.com/jeffsantos/flaskbb` e clique em
   **Fork** para criar seu próprio fork (ou o da dupla).
2. Clone seu fork localmente:

   ```bash
   git clone https://github.com/<seu-usuario>/flaskbb.git
   cd flaskbb
   ```

3. Configure os remotos:

   ```bash
   git remote add upstream-prof https://github.com/jeffsantos/flaskbb.git
   git remote -v
   ```

   O remoto `origin` aponta para o seu fork (onde você empurra seu
   trabalho); `upstream-prof` aponta para o fork do professor (de
   onde você puxa eventuais correções/ajustes didáticos).

## 4. Ambiente Python e dependências

O projeto adota `uv`. A partir da raiz do repositório:

```bash
uv sync
```

Esse comando cria o virtualenv `.venv/` e instala as dependências
fixadas em `uv.lock`. Se preferir não usar `uv`, é possível usar o
`requirements.txt` + `requirements-dev.txt` com `pip` em um
virtualenv tradicional:

```bash
python -m venv .venv
source .venv/bin/activate            # Linux/macOS
.venv\Scripts\activate               # Windows (PowerShell: .venv\Scripts\Activate.ps1)
pip install -r requirements.txt
pip install -r requirements-dev.txt
```

O caminho com `uv` é o recomendado e o que os comandos do `Makefile`
assumem.

## 5. Execução da suíte de testes (baseline)

Antes de tocar em qualquer código, **execute a suíte de testes
existente** e confirme que está 100% verde:

```bash
uv run pytest
```

Ou, usando o `Makefile`:

```bash
make test
```

A baseline esperada é **todos os testes passando**. Se algum teste
falhar no seu ambiente, **registre o problema** (mensagem, sistema
operacional, versão de Python, versão de dependências) e abra
contato com o professor antes de prosseguir — não é esperado que
você "conserte" testes do upstream nesta etapa.

## 6. Execução da aplicação (opcional para a Parte 1)

Não é necessário rodar o servidor para executar os testes, mas é
útil para se familiarizar com o sistema:

```bash
make devconfig    # gera configuração de desenvolvimento
make install      # cria banco, usuário admin etc.
make run          # sobe o servidor em http://localhost:5000
```

## 7. Cobertura de testes

A Parte 1 do projeto pede metas de cobertura para um módulo
específico. Para gerar relatório de cobertura:

```bash
uv run pytest --cov=flaskbb --cov-report=term-missing
```

Para focar em um módulo (ex.: `flaskbb/forum/`):

```bash
uv run pytest --cov=flaskbb.forum --cov-report=term-missing
```

Anote a baseline (% de cobertura por módulo no início da Parte 1) —
ela é o ponto de partida para definir a meta de incremento.

## 8. Estrutura do código relevante

Os módulos que você vai trabalhar nas 3 partes ficam em:

```
flaskbb/
├── forum/         # categorias, fóruns, tópicos, posts (núcleo do domínio)
├── management/    # painel administrativo (usuários, grupos, fóruns)
└── user/          # cadastro, perfis, autenticação básica
```

**Evite** atuar em `flaskbb/plugins/`, `flaskbb/cli/`,
`flaskbb/utils/helpers.py` e demais módulos transversais — eles estão
fora do escopo das tarefas avaliadas.

## 9. Problemas comuns

**`uv` não encontrado.** Verifique a instalação seguindo a
documentação oficial e confirme que o binário está no `PATH`.

**Erro de compilação em dependências nativas (Linux).** Instale os
pacotes de build do sistema (`build-essential`, `libffi-dev`,
`libssl-dev`, `libpq-dev`). No Debian/Ubuntu:
`sudo apt install build-essential libffi-dev libssl-dev libpq-dev`.

**Suíte de testes lenta.** A primeira execução compila/instala
artefatos do `uv`; execuções subsequentes são bem mais rápidas. Use
`uv run pytest -x` para parar no primeiro erro durante depuração.

**Diferenças entre Windows, macOS e Linux.** Caminhos de arquivos,
encoding e algumas dependências nativas podem diferir. Se possível,
prefira WSL2 no Windows para reduzir surpresas.

**Confusão entre upstream e fork do professor.** Lembre que `origin`
é o **seu** fork. As atualizações controladas vêm do
`upstream-prof` (`jeffsantos/flaskbb`), não do `flaskbb/flaskbb`.
