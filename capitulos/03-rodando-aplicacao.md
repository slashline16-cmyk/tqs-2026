# Capítulo 3 — Rodando a aplicação

[⬅ Anterior: Preparando o ambiente](02-preparando-ambiente.md) · [Sumário](../README.md) · Próximo: [Capítulo 4 — Testes automatizados ➡](04-testes-automatizados.md)

---

Com o ambiente preparado, vamos colocar a aplicação Flask no ar **localmente** para entender o que estamos testando. Este capítulo é prático e curto.

## 3.1. Instalar as dependências

Com o ambiente virtual ativo (você deve ver `(.venv)` no prompt):

```bash
pip install -r requirements-dev.txt
```

> `requirements-dev.txt` instala tudo de produção (`requirements.txt`) **mais** as ferramentas de desenvolvimento (pytest, ruff, bandit). Em produção, só instalaríamos `requirements.txt`.

## 3.2. Subir o servidor Flask

```bash
flask --app src.app run
```

Você verá algo como:

```
 * Serving Flask app 'src.app'
 * Running on http://127.0.0.1:5000
```

Abra <http://localhost:5000> no navegador. Você verá o formulário com dois campos (CPF e e-mail) e um botão "Validar".

## 3.3. Testar manualmente

Experimente:

- **CPF válido**: `111.444.777-35` → deve aparecer "CPF: válido" em verde
- **CPF inválido**: `111.444.777-30` (último dígito errado) → "CPF: inválido"
- **E-mail válido**: `aluno@ufopa.edu.br` → "E-mail: válido"
- **E-mail inválido**: `aluno-ufopa.edu.br` (sem `@`) → "E-mail: inválido"

## 3.4. O que está acontecendo por trás?

```
Navegador                  Flask                     Python
───────                    ─────                     ──────
Usuário preenche
campo e clica          ──► POST /validar
                                                ──► validar_cpf("111.444.777-35")
                                                ──► validar_email("aluno@ufopa.edu.br")
                                                ◄── {True, True}
                       ◄── { "cpf_valido": true,
                             "email_valido": true }
JS atualiza o DOM
mostrando "válido"
```

Três arquivos estão envolvidos:

| Arquivo | Papel |
|---|---|
| [`src/app.py`](../src/app.py) | Define as rotas Flask `GET /` (renderiza o HTML) e `POST /validar` (recebe JSON e chama os validadores) |
| [`src/validators.py`](../src/validators.py) | Funções puras `validar_cpf` e `validar_email` — sem dependência de Flask, fáceis de testar isoladamente |
| [`src/templates/index.html`](../src/templates/index.html) | Template Jinja2 com o formulário e o JavaScript que chama `/validar` |

> **Observação**: `validators.py` **não importa Flask**. Essa separação é proposital: a lógica de negócio fica isolada do framework, o que torna os testes mais simples e rápidos. Essa é uma das ideias centrais de arquiteturas como *hexagonal*, *clean architecture* e *ports & adapters*.

## 3.5. Parar o servidor

Pressione `Ctrl+C` no terminal onde o Flask está rodando.

## 3.6. Modo desenvolvimento (auto-reload)

Durante o desenvolvimento, queremos que o servidor recarregue automaticamente quando salvamos um arquivo:

```bash
flask --app src.app run --debug
```

> **Atenção**: nunca rode com `--debug` em produção. Ele expõe o Werkzeug Debugger, que permite executar código arbitrário no servidor. O **bandit** (que veremos no capítulo 6) detecta isso como vulnerabilidade.

---

Próximo capítulo: [Capítulo 4 — Testes automatizados ➡](04-testes-automatizados.md)
