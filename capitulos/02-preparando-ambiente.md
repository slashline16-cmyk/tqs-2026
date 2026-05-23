# Capítulo 2 — Preparando o ambiente

[⬅ Anterior: Visão geral](01-visao-geral.md) · [Sumário](../README.md) · Próximo: [Capítulo 3 — Rodando a aplicação ➡](03-rodando-aplicacao.md)

---

Nesta disciplina vamos trabalhar **inteiramente pelo navegador**, usando o **GitHub Codespaces** — um ambiente Linux completo, com Python, Git e VS Code, rodando na nuvem da Microsoft. Você não vai instalar nada na sua máquina.

> 💡 **E se eu quiser usar outra forma?** Você poderia trabalhar **localmente** (instalando Python, Git e uma IDE) ou usar o **github.dev** (VS Code no navegador, sem terminal e sem execução). Ambas são possíveis, mas não vamos cobrir nesta disciplina. **Padronizar em Codespaces** evita problemas de "no meu computador funciona" e garante que todos os alunos têm o mesmo ambiente.

## 2.1. Pré-requisitos

Para acompanhar esta aula você precisa apenas de:

- Uma conta no [GitHub](https://github.com/signup) (gratuita)
- Um navegador moderno (Chrome, Edge, Firefox, Safari)
- Conexão razoável com a internet

E é só. Nenhum software instalado, nenhum Python, nenhum Git, nenhum SO específico.

> O free tier do GitHub Codespaces oferece **120 horas-core/mês** — mais que suficiente para a disciplina inteira.

## 2.2. Criar o repositório no GitHub

Acesse <https://github.com/new> e preencha o formulário:

1. **Repository name**: `tqs-2026` (ou outro nome de sua escolha).
2. **Description** (opcional): "Projeto da disciplina Teste e Qualidade de Software".
3. **Visibility**: marque **Public** — isso é necessário para o GitHub Pages funcionar no plano gratuito.
4. **Initialize this repository with**: marque **Add a README file** — assim o repositório nasce inicializado e você consegue editar pela web imediatamente.
5. **Add .gitignore** e **Choose a license**: deixe ambos em "None" — vamos criar manualmente para entender cada arquivo.
6. Clique no botão verde **Create repository**.

Você será redirecionado para a página do seu novo repositório, com apenas o `README.md` dentro.

## 2.3. Criar o arquivo `.devcontainer/devcontainer.json`

Esse é o arquivo que diz ao Codespaces **qual ambiente preparar para você**: versão do Python, extensões do VS Code, dependências a instalar automaticamente, portas a expor.

### Passo a passo (interface web do GitHub)

1. Na página do seu repositório, clique no botão **"Add file"** (perto do canto superior direito da lista de arquivos) → **"Create new file"**.

2. No campo de nome de arquivo (no topo), digite **exatamente**:

   ```
   .devcontainer/devcontainer.json
   ```

   > A `/` cria automaticamente a pasta `.devcontainer/`. O ponto no início faz parte do nome (não é separador).

3. Na área de edição (campo grande abaixo), **cole** o conteúdo completo a seguir:

   ```json
   {
       "name": "TQS 2026 - Teste e Qualidade de Software",
       "image": "mcr.microsoft.com/devcontainers/python:1-3.11",
       "postCreateCommand": "pip install -r requirements-dev.txt || true",
       "customizations": {
           "vscode": {
               "extensions": [
                   "ms-python.python",
                   "ms-python.vscode-pylance",
                   "charliermarsh.ruff",
                   "github.vscode-pull-request-github"
               ],
               "settings": {
                   "python.defaultInterpreterPath": "/usr/local/bin/python",
                   "python.testing.pytestEnabled": true,
                   "python.testing.unittestEnabled": false,
                   "editor.formatOnSave": true,
                   "[python]": {
                       "editor.defaultFormatter": "charliermarsh.ruff"
                   }
               }
           }
       },
       "forwardPorts": [5000],
       "portsAttributes": {
           "5000": {
               "label": "Flask app",
               "onAutoForward": "notify"
           }
       }
   }
   ```

4. Role a página para baixo até a seção **"Commit changes"**.

5. Preencha:
   - **Commit message**: `chore: adiciona devcontainer.json para Codespaces`
   - **Extended description** (opcional): pode deixar vazio
   - **Commit directly to the `main` branch** — deixe selecionado

6. Clique no botão verde **"Commit changes"**.

### O que cada linha do arquivo significa

| Linha | Para que serve |
|---|---|
| `"image": ".../python:1-3.11"` | Imagem Docker oficial com Python 3.11 pré-instalado |
| `"postCreateCommand"` | Roda automaticamente após o Codespace ser criado — aqui instala as dependências do projeto |
| `"extensions"` | Extensões do VS Code instaladas automaticamente (Python, Pylance, Ruff, GitHub PRs) |
| `"settings"` | Configurações do editor: testes via pytest, formatar ao salvar usando Ruff |
| `"forwardPorts": [5000]` | Quando o Flask rodar na porta 5000, o Codespace expõe uma URL pública temporária |

> O `|| true` no `postCreateCommand` é proposital: se ainda não houver `requirements-dev.txt` no repositório (caso você esteja começando do zero), o comando falha graciosamente em vez de travar o setup.

## 2.4. Abrir o Codespace

Com o `devcontainer.json` commitado, agora você pode subir o ambiente.

1. Na página principal do repositório, clique no botão verde **"Code"**.
2. Selecione a aba **"Codespaces"** (ao lado de "Local").
3. Clique em **"Create codespace on main"**.

Uma nova aba abre. Aguarde aproximadamente **1 a 2 minutos** na primeira vez (o GitHub cria o container, instala as extensões e roda o `postCreateCommand`). Você verá um **VS Code completo rodando no navegador**.

### Verificar que tudo está pronto

Abra o terminal: tecle **Ctrl + `** (Control + crase) — ou menu **Terminal → New Terminal**. Um terminal abre na parte inferior. Digite:

```bash
python --version
```

Deve responder algo como `Python 3.11.x`. Se aparecer, o ambiente está pronto.

## 2.5. Trabalhando com Codespaces no dia a dia

### Como parar e retomar

- **Para parar** (e economizar cota): vá em <https://github.com/codespaces>, encontre o Codespace na lista e clique nos três pontinhos (`···`) → **"Stop codespace"**. Isso preserva todos os seus arquivos.
- **Para retomar**: na mesma lista, clique no nome do Codespace. Ele acorda em ~10 segundos, exatamente como você deixou (arquivos abertos, terminal, tudo).
- Codespace **parado não consome cota** — só ocupa um pouco de storage (também limitado e generoso).

### Atalhos úteis do VS Code

| Atalho | O que faz |
|---|---|
| `Ctrl + `\` | Abre/foca o terminal |
| `Ctrl + Shift + P` | Paleta de comandos (qualquer ação) |
| `Ctrl + P` | Abrir arquivo pelo nome |
| `Ctrl + Shift + F` | Busca global no projeto |
| `Ctrl + S` | Salvar (formato automático ativa aqui) |

### O que NÃO mudou entre Codespaces e máquina local

Os comandos de terminal são **exatamente os mesmos** que você usaria em uma máquina Linux. Todos os exemplos nos próximos capítulos rodam aqui sem ajuste nenhum:

```bash
pytest                              # roda testes
flask --app src.app run             # sobe o Flask
ruff check .                        # lint
git checkout -b feat/nova-feature   # cria branch
```

## 2.6. Estrutura final do repositório

Ao final desta disciplina, seu repositório terá esta estrutura:

```
tqs-2026/
├── .devcontainer/
│   └── devcontainer.json           # config do Codespaces (criado neste capítulo)
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                  # lint + testes + cobertura
│   │   └── deploy.yml              # deploy GitHub Pages
│   ├── ISSUE_TEMPLATE/             # templates para abrir issues
│   ├── pull_request_template.md    # checklist obrigatório de PRs
│   └── CODEOWNERS                  # quem revisa o quê
├── capitulos/                      # este livro didático
├── src/
│   ├── validators.py               # VERSÃO PRINCIPAL da lógica
│   ├── app.py                      # rotas Flask
│   └── templates/index.html        # formulário renderizado pelo Flask
├── tests/
│   ├── test_validators.py          # testes unitários (4)
│   └── test_app.py                 # testes de integração (2)
├── docs/                           # publicado no GitHub Pages
│   ├── index.html
│   ├── validators.js               # ESPELHO em JS de validators.py
│   └── style.css
├── pyproject.toml                  # config ruff + pytest + coverage
├── requirements.txt                # Flask
├── requirements-dev.txt            # pytest, ruff, bandit, coverage
└── README.md                       # sumário do livro
```

> **De onde vêm esses arquivos?** Os próximos capítulos explicam **o que cada um faz** e o [capítulo 10](10-atividade-pratica.md) traz **código pronto** para você criar/modificar via interface do VS Code dentro do Codespace. Alternativamente, seu professor pode disponibilizar o repositório-modelo já populado para você fazer **fork** — pergunte na primeira aula.

## 2.7. Personalizações antes do primeiro `push`

Se você partiu de um **fork** do repositório-modelo (em vez do passo 2.2), há **dois lugares** com placeholders para trocar pelo seu usuário do GitHub:

- [`docs/index.html`](../docs/index.html) — no rodapé, troque `SEU-USUARIO` na URL `https://github.com/SEU-USUARIO/tqs-2026` pelo seu usuário.
- [`.github/CODEOWNERS`](../.github/CODEOWNERS) — descomente a linha `# * @SEU-USUARIO` e troque pelo seu usuário.

No terminal do Codespace, para encontrar todas as ocorrências:

```bash
grep -rn "SEU-USUARIO" .
```

Faça as substituições, confira que o `grep` não retorna mais nada, faça commit e push.

> Os outros lugares com o nome do professor (LICENSE, README, pyproject.toml) são de **autoria** do material didático e devem ficar como estão — quando você forka, herda a licença MIT e o crédito do autor original.

---

Próximo capítulo: [Capítulo 3 — Rodando a aplicação ➡](03-rodando-aplicacao.md)
