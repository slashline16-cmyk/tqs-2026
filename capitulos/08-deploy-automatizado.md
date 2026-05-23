# Capítulo 8 — Deploy automatizado

[⬅ Anterior: CI com GitHub Actions](07-ci-github-actions.md) · [Sumário](../README.md) · Próximo: [Capítulo 9 — Pull Requests ➡](09-pull-requests.md)

---

Testes verdes não bastam — alguém precisa **usar** a aplicação. Vamos automatizar o **deploy** para que toda mudança em `main` apareça publicada na web em poucos minutos, sem ninguém ter que rodar comando algum.

## 8.1. Por que GitHub Pages?

| Vantagem | Detalhe |
|---|---|
| **Gratuito** | Para repositórios públicos, sem custo |
| **Integrado ao GitHub** | Workflow oficial pronto, sem cadastro em outro serviço |
| **Suficiente para o demo** | Hospeda HTML+CSS+JS, que é o suficiente para o frontend do nosso validador |

**Limitação importante**: GitHub Pages só serve **arquivos estáticos**. O Flask **não roda lá**. Por isso a lógica do validador também existe em JavaScript (capítulo 1) — para o demo público funcionar sem servidor.

## 8.2. O que vai para o Pages

Apenas a pasta [`docs/`](../docs/) do repositório:

```
docs/
├── index.html          # formulário e UI
├── style.css           # estilo (paleta da UFOPA)
└── validators.js       # espelho JS de src/validators.py
```

A pasta `docs/` é uma escolha de convenção: muitos projetos usam a branch `gh-pages` em vez disso, mas pasta na própria branch é mais simples.

## 8.3. O workflow `deploy.yml`

Veja o arquivo completo em [`.github/workflows/deploy.yml`](../.github/workflows/deploy.yml).

### Quando dispara

```yaml
on:
  push:
    branches: [main]
  workflow_dispatch:
```

- A cada `push` na `main` (após merge de PR, por exemplo)
- Manualmente via botão **"Run workflow"** na aba Actions (`workflow_dispatch`)

### Permissões necessárias

```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```

Sem essas permissões, o GitHub não autoriza a publicação no Pages. Esse é o "Login Sem Senha" entre GitHub Actions e GitHub Pages, usando *OIDC*.

### Concorrência

```yaml
concurrency:
  group: pages
  cancel-in-progress: false
```

Garante que **só um deploy roda por vez**. Se dois pushes acontecerem em sequência, o segundo espera o primeiro terminar — evita estado inconsistente no Pages.

### Os dois jobs

**Job 1 — build**:

```yaml
build:
  steps:
    - uses: actions/checkout@v4
    - uses: actions/configure-pages@v5
    - uses: actions/upload-pages-artifact@v3
      with:
        path: docs
```

Empacota `docs/` num artifact que o segundo job vai consumir.

**Job 2 — deploy**:

```yaml
deploy:
  needs: build
  environment:
    name: github-pages
    url: ${{ steps.deployment.outputs.page_url }}
  steps:
    - id: deployment
      uses: actions/deploy-pages@v4
```

Publica o artifact e devolve a URL final no resultado do job. Você verá um link clicável na aba Actions ao final.

## 8.4. Ativando o GitHub Pages no repositório

Antes do primeiro deploy funcionar, **uma configuração manual** (uma vez só):

1. No GitHub, vá em **Settings → Pages** (menu lateral esquerdo).
2. Em **Build and deployment → Source**, selecione **GitHub Actions** (em vez do padrão "Deploy from a branch").
3. Salve.
4. Faça qualquer push para `main`.
5. Aguarde o workflow **Deploy GitHub Pages** rodar verde na aba Actions.
6. Acesse a URL final, que será algo como `https://<seu-usuario>.github.io/tqs-2026/`.

> Na primeira vez pode demorar alguns minutos para o DNS propagar. Tome um café.

## 8.5. Confirmando que está no ar

Abra a URL no navegador. Você deve ver:

- O título "Teste e Qualidade de Software - PC010027"
- O formulário com CPF e e-mail
- A aviso explicando que essa é a versão estática

Teste com `111.444.777-35` (válido) e `111.444.777-30` (inválido) — a validação acontece **inteiramente no navegador**, sem comunicação com servidor.

## 8.6. Encadeando CI e deploy (opcional)

No estado atual, o deploy roda **mesmo se o CI estiver vermelho**. Para fazer o deploy **depender** do CI, mude o gatilho:

```yaml
on:
  workflow_run:
    workflows: ["CI"]
    types: [completed]
    branches: [main]

jobs:
  build:
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
```

Não fizemos isso aqui para manter os dois workflows independentes — fica como exercício para times maduros.

## 8.7. Outras opções de deploy

Se quiser explorar além de GitHub Pages (por exemplo, para deployar o **Flask** de verdade):

| Serviço | Free tier? | Suporta Python? | Observação |
|---|---|---|---|
| [Render](https://render.com/) | ✅ | ✅ | Deploy a partir do GitHub via webhook |
| [Railway](https://railway.app/) | Limitado | ✅ | Excelente DX |
| [Fly.io](https://fly.io/) | ✅ (com limites) | ✅ | Requer Dockerfile |
| [Vercel](https://vercel.com/) | ✅ | Via serverless | Foco em Node.js mas suporta Python |

Esses ficam como leitura adicional.

---

Próximo capítulo: [Capítulo 9 — Pull Requests ➡](09-pull-requests.md)
