# Capítulo 9 — Pull Requests

[⬅ Anterior: Deploy automatizado](08-deploy-automatizado.md) · [Sumário](../README.md) · Próximo: [Capítulo 10 — Atividade prática ➡](10-atividade-pratica.md)

---

Até aqui, tudo o que fizemos foi pessoal. Em **time**, o GitHub é o ponto de encontro: cada mudança vira um **Pull Request** que é discutido, revisado e mergeado. Este capítulo cobre o workflow recomendado, as configurações que **forçam** a qualidade, e o básico de code review.

## 9.1. Fluxo recomendado, do começo ao fim

Vamos simular: "preciso adicionar um validador de CNPJ".

### Passo 1 — Crie uma branch a partir da `main`

```bash
git checkout main
git pull                          # garante que está na versão mais recente
git checkout -b feat/validar-cnpj
```

> **Convenção**: prefixos como `feat/`, `fix/`, `docs/`, `refactor/`, `chore/` deixam claro o tipo de mudança. Não é obrigatório, mas ajuda a leitura do histórico.

### Passo 2 — Faça as mudanças seguindo TDD

```python
# tests/test_validators.py
def test_aceita_cnpj_valido():
    assert validar_cnpj("11.222.333/0001-81") is True
```

Roda → falha (função não existe). Implementa o mínimo. Refatora. (Capítulo 5.)

### Passo 3 — Garanta que tudo está verde localmente

```bash
ruff check . && ruff format --check .
bandit -r src/
pytest --cov=src --cov-fail-under=80
```

Se algo falhar, **conserte antes de comitar**. Você evita um round-trip com o CI.

### Passo 4 — Commit e push

```bash
git add .
git commit -m "feat: adiciona validador de CNPJ"
git push -u origin feat/validar-cnpj
```

> O `-u` (ou `--set-upstream`) só precisa ser usado no primeiro push da branch.

### Passo 5 — Abra o Pull Request

O GitHub vai mostrar um banner: **"Compare & pull request"**. Clique.

O **template de PR** ([`.github/pull_request_template.md`](../.github/pull_request_template.md)) aparece automaticamente no campo de descrição. Preencha:

- **Descrição**: por quê dessa mudança?
- **Tipo de mudança**: feat / fix / refactor / docs / CI
- **Checklist de qualidade**: cada item conferido
- **Como testar**: passos para o revisor reproduzir

### Passo 6 — Aguarde o CI

Em alguns minutos, o GitHub Actions vai rodar e mostrar:

- ✅ **All checks have passed** → pode pedir revisão
- ❌ **Some checks were not successful** → veja o que falhou, conserte, faça novo commit

> Cada novo commit na branch **re-dispara o CI** automaticamente.

### Passo 7 — Code review

Marque um revisor (ou aguarde alguém da equipe pegar). O revisor pode:

- **Comentar** em linhas específicas pedindo mudanças
- **Aprovar** o PR
- **Solicitar mudanças** (bloqueando o merge até serem feitas)

### Passo 8 — Merge

Quando o CI estiver verde e a revisão aprovada, clique em **Squash and merge** (recomendado para projetos pequenos — vira um único commit limpo na `main`).

### Passo 9 — Deletar a branch

O GitHub oferece um botão **"Delete branch"** logo após o merge. Use-o — branches mortas poluem.

## 9.2. Ativando *branch protection* na `main`

Para forçar que **toda mudança passe por PR + CI verde**, e ninguém (nem o admin) consiga dar push direto na `main`:

1. **Settings → Branches → Add branch protection rule**.
2. **Branch name pattern**: `main`.
3. Marque:
   - ☑ **Require a pull request before merging**
     - ☑ Require approvals: 1 (se for time)
   - ☑ **Require status checks to pass before merging**
     - ☑ Require branches to be up to date before merging
     - Selecione o job **`qualidade-e-testes`** (o nome vem de `ci.yml`)
   - ☑ **Do not allow bypassing the above settings**
4. **Create**.

> A partir daí, push direto na `main` é **bloqueado**. Toda mudança vira PR, e todo PR só mergeia com CI verde. **O processo virou portão de segurança automático.**

## 9.3. Templates de issue

Em [`.github/ISSUE_TEMPLATE/`](../.github/ISSUE_TEMPLATE/), há dois templates pré-preenchidos:

- **bug_report.md** — pede passos para reproduzir, ambiente, comportamento esperado vs observado
- **feature_request.md** — pede motivação, solução proposta, alternativas consideradas

Quando alguém clica em "New Issue", o GitHub oferece esses templates. Resultado: issues organizadas e fáceis de triar.

## 9.4. CODEOWNERS

[`.github/CODEOWNERS`](../.github/CODEOWNERS) define quem é automaticamente atribuído como revisor de cada PR. No template do projeto, a regra vem **comentada** — você precisa descomentar e colocar seu usuário:

```
* @SEU-USUARIO
```

`*` significa "qualquer arquivo". Em projetos maiores, você teria regras por pasta:

```
src/auth/      @time-de-seguranca
docs/          @time-de-conteudo
.github/       @time-de-infra
```

## 9.5. Code review básico — perguntas a se fazer

Ao revisar um PR, **passe pelas perguntas nessa ordem**:

1. **Faz o que diz que faz?** A descrição bate com o diff?
2. **Tem testes?** Eles cobrem o caminho feliz **e** os casos de borda?
3. **A cobertura caiu?** Se sim, alguma justificativa?
4. **O nome das funções/variáveis explica o que elas fazem?** Você precisaria de comentários?
5. **Há código morto, comentários esquecidos, prints de debug, TODOs vagos?**
6. **A mudança é proporcional ao problema?** Pequena correção de bug com 500 linhas refatoradas é sinal vermelho.

> **Cultura do review**: comente **o código**, não a pessoa. "Esse `if` poderia virar `dict.get(key, default)`" é diferente de "você não sabe usar `dict.get`". Diferença grande no clima do time.

## 9.6. Quando *não* mergear

Mesmo com CI verde e aprovação:

- Se a mudança gera **conflito** com `main`, peça ao autor para fazer `git merge main` (ou `git rebase main`) primeiro.
- Se está **sexta às 18h** e a mudança é grande, **espere segunda**. Mergear coisa grande às vésperas do final de semana é receita para incêndio.
- Se faltou conversa de design, peça para abrir uma **discussão** antes do PR seguir.

---

Próximo capítulo: [Capítulo 10 — Atividade prática ➡](10-atividade-pratica.md)
