# Capítulo 6 — Qualidade de código

[⬅ Anterior: TDD na prática](05-tdd-na-pratica.md) · [Sumário](../README.md) · Próximo: [Capítulo 7 — CI com GitHub Actions ➡](07-ci-github-actions.md)

---

Testes verificam **se o código faz o que deveria**. **Qualidade de código** vai além: pergunta se o código está **limpo, padronizado e seguro** — qualidades que não aparecem num teste de aceitação, mas que afetam manutenibilidade e risco.

## 6.1. O kit de qualidade deste projeto

Quatro ferramentas, cada uma com um papel distinto:

| Ferramenta | O que faz | Por que importa |
|---|---|---|
| **ruff check** | Detecta variáveis não usadas, imports redundantes, complexidade exagerada, smell de código | Código padronizado é mais fácil de revisar e manter |
| **ruff format** | Formata automaticamente (estilo Black) | Acaba com discussões de "tabs vs spaces" no PR |
| **bandit** | Procura padrões inseguros de código Python | Pega bugs como `eval`, senhas hardcoded, SQL injection, debug ativo em produção |
| **pytest-cov** | Mede que % das linhas de `src/` é executada pelos testes | Cobertura **alta não garante** código correto, mas cobertura **baixa garante** código não testado |

## 6.2. Rotina antes de cada commit

```bash
ruff check .             # lint
ruff format .            # formata automaticamente
bandit -r src/           # análise de segurança
pytest --cov=src --cov-fail-under=80
```

Se algum desses passos falhar, **conserte antes de fazer commit**. Isso evita que o CI te pegue depois e gera muito menos retrabalho.

## 6.3. Lint na prática

**Lint** = análise estática do código que aponta problemas sem rodá-lo. Exemplos típicos do `ruff`:

```text
src/validators.py:13:45: B905 [*] `zip()` without an explicit `strict=` parameter
```

Isso significa: na linha 13, coluna 45, você usou `zip()` sem dizer se quer comportamento estrito (erro se as listas tiverem tamanhos diferentes). O `[*]` indica que o ruff sabe corrigir automaticamente:

```bash
ruff check . --fix
```

Ele pergunta antes de aplicar (modo seguro) e mostra o diff.

## 6.4. Format na prática

`ruff format` muda **apenas** a formatação (espaços, quebras de linha, aspas) — nunca a semântica. Rode sempre antes do commit:

```bash
ruff format .
```

E no CI, usamos a versão `--check` (não modifica, só falha se algo está fora do padrão):

```bash
ruff format --check .
```

> **Por que ter formatador automático?** Acaba com debates eternos de estilo. A regra é "o que o formatter cuspir é a verdade". Todos no time formatam o código da mesma maneira, sem esforço mental.

## 6.5. Análise de segurança com bandit

`bandit` escaneia o código Python procurando padrões conhecidos de vulnerabilidade. Exemplo:

```text
>> Issue: [B201:flask_debug_true] A Flask app appears to be run with debug=True,
   which exposes the Werkzeug debugger and allows the execution of arbitrary code.
   Severity: High   Confidence: Medium
```

Isso aconteceu de verdade neste projeto na primeira versão. O `bandit` pegou — e por isso a versão final de [`src/app.py`](../src/app.py) chama `app.run()` sem `debug=True`.

> **Atenção**: `bandit` é análise **estática**, não substitui revisão humana nem testes de segurança ativos (penetration testing). Mas elimina uma classe inteira de erros bobos antes do PR.

## 6.6. Cobertura como portão (mas não como meta)

```bash
pytest --cov=src --cov-fail-under=80
```

Configuramos o pipeline para **falhar** se a cobertura cair abaixo de **80%**. Hoje estamos em **82%** — confortável, mas com espaço para crescer conforme a suíte de testes evolui.

**Mas cobertura não é meta**, é **piso**:

- Cobertura **alta** não garante testes bons (já vimos no capítulo 4 que `validar_cpf("123")` sem `assert` cobre a função sem testar nada).
- Cobertura **baixa** garante que existe código sem nenhum teste. É um sinal vermelho garantido.

Pense assim: a meta é "para cada caso de uso e cada caso de borda, existe um teste". A cobertura é um **indicador indireto** disso, não o objetivo final.

> Os exercícios do [capítulo 10](10-atividade-pratica.md) vão te levar a 90%+ — basta seguir TDD e cobrir os casos de borda que hoje estão descobertos (linhas 21, 26, 29 e 39 de `src/validators.py`).

## 6.7. Configuração centralizada

Toda a configuração das ferramentas vive em [`pyproject.toml`](../pyproject.toml):

```toml
[tool.ruff]
line-length = 100
target-version = "py311"
extend-exclude = ["docs", ".venv"]

[tool.ruff.lint]
select = ["E", "W", "F", "I", "B", "UP", "SIM"]

[tool.pytest.ini_options]
minversion = "8.0"
addopts = "-ra --strict-markers"
testpaths = ["tests"]
pythonpath = ["."]

[tool.coverage.run]
source = ["src"]
branch = true
```

> **Vantagem**: um único arquivo de config. Quem chegar no projeto sabe onde olhar para entender as regras.

## 6.8. Pre-commit (opcional, recomendado)

Se quiser **automatizar** essa rotina de qualidade no próprio Git (para que o commit não aconteça se algo falhar), instale o [pre-commit](https://pre-commit.com/). Não está configurado neste projeto para manter a simplicidade, mas é o próximo passo natural para times maduros.

---

Próximo capítulo: [Capítulo 7 — CI com GitHub Actions ➡](07-ci-github-actions.md)
