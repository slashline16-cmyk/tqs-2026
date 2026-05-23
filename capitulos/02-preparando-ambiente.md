# Capítulo 2 — Preparando o ambiente

[⬅ Anterior: Visão geral](01-visao-geral.md) · [Sumário](../README.md) · Próximo: [Capítulo 3 — Rodando a aplicação ➡](03-rodando-aplicacao.md)

---

Antes de escrever uma linha de código, precisamos ter as ferramentas certas instaladas. Este capítulo cobre **pré-requisitos**, **criação do repositório no GitHub** e **estrutura inicial do projeto**.

## 2.1. Pré-requisitos

- **Git** ([download](https://git-scm.com/downloads))
- **Python 3.11+** ([download](https://www.python.org/downloads/))
- Uma conta no **GitHub** ([criar](https://github.com/signup))
- Uma **IDE** (Ambiente de Desenvolvimento Integrado) à sua escolha:
  - [Visual Studio Code](https://code.visualstudio.com/) — gratuito, com extensões oficiais para Python, GitHub e Live Server
  - [Antigravity IDE](https://antigravity.google/) — IDE da Google com agentes de IA integrados
  - [Cursor](https://cursor.com/) — fork do VS Code com IA nativa para edição e refatoração
  - Qualquer outra (PyCharm, Sublime Text, Neovim) — a escolha é sua

> **Por que uma IDE?** O CI vai detectar erros depois do `push`, mas é muito mais rápido encontrá-los enquanto você digita. A IDE marca import quebrado, função não definida, erro de sintaxe e linha desformatada **em tempo real**. Para Python, recomendamos pelo menos as extensões oficiais **Python**, **Pylance** e **Ruff** no VS Code (Cursor e Antigravity herdam o mesmo ecossistema de extensões).

Confira no terminal:

```bash
git --version
python3 --version
code --version          # se usar VS Code / Cursor / Antigravity
```

## 2.2. Criar o repositório no GitHub

1. Acesse <https://github.com/new>.
2. **Repository name**: `tqs-2026` (ou outro nome de sua escolha).
3. Marque como **Public** — necessário para o GitHub Pages funcionar no plano gratuito.
4. **Não** marque "Add a README", "Add .gitignore" nem "Choose a license" — vamos criar tudo localmente para entender cada arquivo.
5. Clique em **Create repository**.

## 2.3. Inicializar o projeto localmente

No terminal:

```bash
mkdir tqs-2026 && cd tqs-2026
git init -b main
git remote add origin https://github.com/<seu-usuario>/tqs-2026.git
```

> Substitua `<seu-usuario>` pelo seu nome de usuário do GitHub.

## 2.4. Criar a estrutura de pastas

```bash
mkdir -p .github/workflows .github/ISSUE_TEMPLATE src/templates tests docs capitulos
```

Cada pasta tem um papel específico — vamos conhecê-los na próxima seção.

## 2.5. Criar o ambiente virtual

Um **ambiente virtual** (venv) isola as dependências deste projeto das outras instalações de Python do seu sistema. Sempre crie um por projeto.

```bash
python3 -m venv .venv
source .venv/bin/activate          # Linux/Mac
# .venv\Scripts\activate           # Windows
```

Você saberá que o ambiente está ativo quando o prompt do terminal mostrar `(.venv)` no início.

## 2.6. Estrutura final do repositório

A organização que vamos construir nos próximos capítulos:

```
tqs-2026/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                  # lint + testes + cobertura
│   │   └── deploy.yml              # deploy GitHub Pages
│   ├── ISSUE_TEMPLATE/             # templates para abrir issues
│   ├── pull_request_template.md    # checklist obrigatório de PRs
│   └── CODEOWNERS                  # quem revisa o quê
├── capitulos/                      # este livro didático
├── src/
│   ├── validators.py               # FONTE CANÔNICA da lógica de validação
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

Não precisa criar todos os arquivos agora — eles vão nascendo conforme avançamos.

## 2.7. Personalizações antes do primeiro `push`

Se você está usando este projeto a partir de um **fork** (ou copiou os arquivos para o seu próprio repo), há **dois lugares** com placeholders que você precisa trocar pelo seu nome de usuário do GitHub:

- [`docs/index.html`](../docs/index.html) — no rodapé, troque `SEU-USUARIO` na URL `https://github.com/SEU-USUARIO/tqs-2026` pelo seu usuário (assim o link do demo público aponta para o seu repo, não o do professor).
- [`.github/CODEOWNERS`](../.github/CODEOWNERS) — descomente a linha `# * @SEU-USUARIO` e troque pelo seu usuário (assim o GitHub te atribui automaticamente como revisor dos PRs).

Para encontrar tudo de uma vez:

```bash
grep -rn "SEU-USUARIO" .
```

Faça as substituições, confira com `grep` de novo (saída vazia = tudo certo), e só então faça o `push`.

> Os outros lugares com o nome do professor (LICENSE, README, pyproject.toml) são de **autoria** do material didático e devem ficar como estão — quando você forka, você herda a licença MIT e o crédito do autor original.

---

Próximo capítulo: [Capítulo 3 — Rodando a aplicação ➡](03-rodando-aplicacao.md)
