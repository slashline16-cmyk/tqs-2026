# Capítulo 10 — Atividade prática

[⬅ Anterior: Pull Requests](09-pull-requests.md) · [Sumário](../README.md) · Próximo: [Capítulo 11 — Recursos & Licença ➡](11-recursos-e-licenca.md)

---

Você leu os 9 capítulos anteriores — agora é hora de **fazer**. Esta lista de tarefas exercita, em conjunto, tudo que foi visto: TDD, cobertura, lint, PR, branch protection, CI.

## Tarefa 1 — Validador de CNPJ via TDD

**Objetivo**: implementar `validar_cnpj(cnpj: str) -> bool` em [`src/validators.py`](../src/validators.py), **escrevendo os testes primeiro** (TDD do capítulo 5).

### Sobre o algoritmo

O CNPJ tem 14 dígitos. Os dois últimos são dígitos verificadores calculados de forma semelhante ao CPF, mas com **pesos diferentes**:

- Primeiro dígito verificador: pesos `[5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2]` (12 dígitos)
- Segundo dígito verificador: pesos `[6, 5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2]` (13 dígitos)
- Soma os produtos, divide por 11, pega o resto: se for menor que 2, o dígito é 0; senão, é `11 - resto`

CNPJ válido para testes: `11.222.333/0001-81`.

### Critérios de aceitação

- [ ] Mínimo de **6 testes parametrizados** cobrindo: válido com máscara, válido sem máscara, tamanho errado, todos dígitos iguais, dígito verificador errado, entrada não-string
- [ ] Cobertura final do projeto **≥ 90%** (ajuste `--cov-fail-under` no `ci.yml` para validar isso)
- [ ] PR seguindo o template, CI verde antes do merge
- [ ] Botão equivalente para CNPJ no formulário ([`src/templates/index.html`](../src/templates/index.html) e [`docs/index.html`](../docs/index.html))
- [ ] Implementação espelhada em [`docs/validators.js`](../docs/validators.js)
- [ ] Branch nomeada `feat/validar-cnpj`

### Dica

Comece pelo teste **mais simples** ("aceita um CNPJ válido com máscara") e use o ciclo Red → Green → Refactor. Não tente codar o algoritmo todo de uma vez.

## Tarefa 2 — Validar telefone

**Objetivo**: adicionar um campo de telefone celular brasileiro no formulário.

### Especificação

- Formato aceito: `(99) 99999-9999` ou `99999999999` (11 dígitos)
- O nono dígito (logo após o DDD) **deve ser 9**
- DDDs válidos: 11 a 99 (não validar lista oficial — só a faixa)

### Critérios de aceitação

- [ ] Função `validar_telefone` em [`src/validators.py`](../src/validators.py) com testes
- [ ] Versão JS em [`docs/validators.js`](../docs/validators.js)
- [ ] Novo campo no formulário (Flask e estático)
- [ ] Endpoint `/validar` retorna também `telefone_valido` no JSON
- [ ] Teste de integração em [`tests/test_app.py`](../tests/test_app.py) cobrindo o novo campo
- [ ] Cobertura mantida **≥ 90%**

## Tarefa 3 — Quebrar o CI de propósito (e consertar)

**Objetivo**: passar pela experiência de ver o CI ficar vermelho **antes** que aconteça num projeto real.

### Passos

1. Em uma branch de teste (`chore/quebrando-ci`):
   - Apague um caractere de [`src/validators.py`](../src/validators.py) (ex.: remova o `:` de uma assinatura de função).
2. Commit + push.
3. Abra um PR (não precisa mergear).
4. Espere o CI rodar e **ficar vermelho**.
5. Vá na aba **Actions** → clique no run → clique no job → expanda o passo vermelho.
6. **Leia o log de baixo para cima** e identifique o erro real.
7. Conserte localmente, faça novo commit, observe o CI ficar verde.
8. **Não mergeie esse PR** — feche sem merge.

### Reflexão (escreva em um comentário do PR)

- Qual passo do pipeline falhou primeiro?
- Os outros passos chegaram a rodar?
- Em quanto tempo o CI te avisou?
- Quanto tempo levaria se você dependesse de outro desenvolvedor rodar manualmente?

> **Por quê?** A primeira vez que o CI falha em um projeto que você se importa é estressante. Quanto antes você passar por isso num **ambiente controlado**, melhor.

## Tarefa 4 (bônus) — Ativar branch protection e tentar burlar

1. Ative branch protection na `main` (capítulo 9.2).
2. Tente fazer um `git push origin main` direto, com mudança trivial.
3. Anote o erro que o GitHub retorna.
4. Repita o processo via PR.
5. Conclua: que classe de erros essa proteção **impede que aconteça**?

## Tarefa 5 (desafio) — Mutation testing

Instale [`mutmut`](https://mutmut.readthedocs.io/) e rode contra `src/validators.py`:

```bash
pip install mutmut
mutmut run --paths-to-mutate src/validators.py
mutmut results
```

Ele vai modificar seu código (trocar `==` por `!=`, `+` por `-`, etc.) e ver se algum teste pega. Se nenhum teste pegar, seu teste é fraco — mesmo com 100% de cobertura.

Discuta com os colegas: **por que cobertura alta não bastou?**

---

## Como entregar

Cada tarefa deve virar **um PR separado** no seu fork ou repositório pessoal:

- PR descritivo, seguindo o template
- CI verde
- Pelo menos uma auto-revisão (comentários do próprio autor no PR explicando decisões)

Para tarefa 3, anexe o link do PR (mesmo fechado sem merge) ao seu relatório.

---

Próximo capítulo: [Capítulo 11 — Recursos & Licença ➡](11-recursos-e-licenca.md)
