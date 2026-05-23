# Capítulo 4 — Testes automatizados

[⬅ Anterior: Rodando a aplicação](03-rodando-aplicacao.md) · [Sumário](../README.md) · Próximo: [Capítulo 5 — TDD na prática ➡](05-tdd-na-pratica.md)

---

A aplicação roda — mas como sabemos que ela está **correta**? E como garantir que continue correta depois de futuras mudanças? Esta é a pergunta que **testes automatizados** respondem.

## 4.1. Dois tipos de teste neste projeto

| Tipo | O que testa | Onde está | Quantos |
|---|---|---|---|
| **Unitário** | Funções isoladas (`validar_cpf`, `validar_email`) | [`tests/test_validators.py`](../tests/test_validators.py) | 4 |
| **Integração** | Rotas Flask reais (`GET /`, `POST /validar`) | [`tests/test_app.py`](../tests/test_app.py) | 2 |

> **Diferença**: o teste unitário não envolve nada além da função. O teste de integração levanta o cliente HTTP do Flask, simula uma requisição completa e verifica a resposta. **Os dois são úteis** — o unitário é rápido e específico; o de integração garante que tudo "se encontra" corretamente.

> **Suíte enxuta de propósito.** Mantemos só **1 caso válido + 1 caso inválido** por validador. O objetivo é que você leia o código de teste inteiro em segundos e entenda exatamente o que está acontecendo. No [capítulo 10](10-atividade-pratica.md) você vai **adicionar** testes de casos de borda como exercício.

## 4.2. Como é um teste unitário aqui

Olhe [`tests/test_validators.py`](../tests/test_validators.py). Cada teste é uma função que começa com `test_`:

```python
def test_aceita_cpf_valido():
    assert validar_cpf("111.444.777-35") is True


def test_rejeita_cpf_com_digito_verificador_errado():
    assert validar_cpf("111.444.777-30") is False
```

A estrutura mínima é:

1. **Arrange** — preparar a entrada (`"111.444.777-35"`)
2. **Act** — chamar a função (`validar_cpf(...)`)
3. **Assert** — verificar o resultado (`is True`)

Nesses exemplos, o passo Arrange é tão simples que cabe na própria chamada.

## 4.3. Como é um teste de integração

Em [`tests/test_app.py`](../tests/test_app.py):

```python
@pytest.fixture
def client():
    app = criar_app()
    return app.test_client()


def test_validar_retorna_resultados_corretos(client):
    resposta = client.post(
        "/validar",
        json={"cpf": "111.444.777-35", "email": "aluno@ufopa.edu.br"},
    )
    assert resposta.get_json() == {"cpf_valido": True, "email_valido": True}
```

A **fixture** `client` cria uma instância isolada do app Flask para cada teste. Não sobe servidor, não abre porta — tudo acontece em memória. É **rápido** e **isolado** (um teste não interfere no outro).

## 4.4. Rodar todos os testes

```bash
pytest
```

Saída esperada:

```
collected 6 items

tests/test_app.py ..                                               [ 33%]
tests/test_validators.py ....                                      [100%]

============================== 6 passed in 0.18s ==============================
```

Um ponto `.` por teste que passou. Um `F` apareceria por teste que falhou.

## 4.5. Rodar um arquivo específico

```bash
pytest tests/test_validators.py
```

## 4.6. Rodar um teste específico

```bash
pytest tests/test_validators.py::test_aceita_cpf_valido
```

Esse formato é `arquivo::nome_da_função`. Útil quando você está desenvolvendo um teste específico e não quer esperar todos os outros.

## 4.7. Modo verbose

```bash
pytest -v
```

Lista cada teste com nome completo, ótimo para depurar.

## 4.8. Parar no primeiro erro

```bash
pytest -x
```

Quando você está corrigindo bugs em sequência, isso evita que outros testes "falsos-positivos" poluam a saída.

## 4.9. Cobertura de código

**Cobertura** = % das linhas do código de produção (`src/`) que são executadas pelos testes. Quanto maior, mais difícil é uma mudança quebrar algo sem que algum teste perceba.

```bash
pytest --cov=src --cov-report=term-missing
```

Saída:

```
Name                Stmts   Miss Branch BrPart  Cover   Missing
---------------------------------------------------------------
src/__init__.py         0      0      0      0   100%
src/app.py             15      0      0      0   100%
src/validators.py      22      4      8      4    73%   21, 26, 29, 39
---------------------------------------------------------------
TOTAL                  37      4      8      4    82%
```

A coluna `Missing` aponta as linhas **não exercitadas** pelos testes. As linhas 21, 26, 29 e 39 de `validators.py` são os **guards** (entrada não-string, CPF com todos dígitos iguais, e-mail vazio) — comportamentos que a função trata corretamente, mas que **nenhum teste atual exercita**.

> Veja só: a função é mais defensiva do que os testes — isso é um cheiro de "falta cobrir mais casos". É exatamente isso que a [atividade prática](10-atividade-pratica.md) vai te pedir.

### Relatório HTML (navegável)

```bash
pytest --cov=src --cov-report=html
python -m http.server 8000 -d htmlcov
```

O Codespace vai detectar a porta 8000 e mostrar um pop-up para abrir no navegador. Uma página abre listando os arquivos cobertos — clique em qualquer um para ver linhas cobertas (verde) e descobertas (vermelho).

Para parar o servidor HTTP: `Ctrl + C` no terminal.

## 4.10. Meta de cobertura neste projeto

Configuramos o pipeline para falhar se a cobertura cair abaixo de **80%**:

```bash
pytest --cov=src --cov-fail-under=80
```

Hoje estamos em **82%** — confortavelmente acima do piso, mas com **espaço para crescer**. À medida que você adiciona casos de borda (capítulo 10), a cobertura sobe.

## 4.11. Cobertura ≠ qualidade

Cobertura alta **não garante** que os testes sejam bons. É perfeitamente possível ter 100% de cobertura com testes que não verificam nada:

```python
def test_inutil():
    validar_cpf("123")     # executa a função, mas não checa o resultado
```

A cobertura sobe, mas o teste é mentira. **Mutation testing** (capítulo 11) ajuda a expor esse tipo de problema.

> **Regra prática**: cobertura **alta** é necessária mas não suficiente. Cobertura **baixa** é prova de código não testado.

## 4.12. Próximos passos

Quando sua suíte crescer, vale aprender:

- **`@pytest.mark.parametrize`** — uma única função de teste que roda com várias entradas, sem duplicação ([docs](https://docs.pytest.org/en/stable/how-to/parametrize.html))
- **`@pytest.fixture`** — você já viu uma! Compartilha setup entre testes
- **`pytest -k <expressão>`** — roda só os testes cujos nomes batem com a expressão

---

Próximo capítulo: [Capítulo 5 — TDD na prática ➡](05-tdd-na-pratica.md)
