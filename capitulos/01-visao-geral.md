# Capítulo 1 — Visão geral

[⬅ Sumário](../README.md) · Próximo: [Capítulo 2 — Preparando o ambiente ➡](02-preparando-ambiente.md)

---

Antes de pôr a mão na massa, vale entender **o que** vamos construir e **por que** as escolhas foram feitas dessa maneira. Este capítulo é curto e contextual — ele responde "por que estou olhando para este repositório?".

## 1.1. A aplicação

Vamos construir um **validador de CPF e e-mail** com formulário web. Parece simples, mas foi escolhido a dedo porque reúne todos os ingredientes que queremos demonstrar:

- A regra de **CPF** tem **casos de borda reais** (tamanho errado, todos os dígitos iguais, dígitos verificadores que precisam ser calculados). É o cenário perfeito para TDD.
- O **e-mail** mostra como usar **regex** e como decidir até onde levar o rigor da validação (RFC 5322 completa? regex prática? validar com o servidor SMTP?).
- A aplicação é pequena o bastante para um aluno **reproduzir do zero em uma aula**, mas rica o bastante para mostrar testes unitários, testes de integração, CI, deploy e workflow de PR.

## 1.2. Decisão arquitetural: dois lugares espelhados

A mesma regra de negócio vive em **dois arquivos**, em duas linguagens:

| Lugar | Para que serve | Onde está |
|---|---|---|
| **Python** (canônico) | Implementação testada com pytest. **Toda mudança começa aqui.** | [`src/validators.py`](../src/validators.py) |
| **JavaScript** (espelho) | Permite o demo interativo no GitHub Pages (que só hospeda arquivos estáticos — Flask não roda lá). | [`docs/validators.js`](../docs/validators.js) |

> **Lição didática**: a mesma regra de negócio pode ser implementada e testada em qualquer linguagem. O que importa é a **regra**, não o framework.
>
> Em projetos reais, isso seria um problema (duplicação). Aqui é proposital: mostra como, na prática, uma equipe garante que o backend e o frontend concordem (por exemplo, com **testes de contrato** ou geração de código a partir de uma especificação OpenAPI). Esse tema voltará em aulas futuras.

## 1.3. Tecnologias usadas

| Camada | Ferramenta | Versão |
|---|---|---|
| Linguagem | Python | 3.11+ |
| Web framework | Flask | 3.0 |
| Testes | pytest + pytest-cov | 8.3 / 5.0 |
| Lint e formatação | ruff | 0.6 |
| Segurança estática | bandit | 1.9 |
| CI/CD | GitHub Actions | — |
| Hospedagem do demo | GitHub Pages | — |

Cada uma dessas ferramentas será explicada **no momento em que entrar em cena** nos próximos capítulos. Não precisa estudar todas agora — basta saber que existem.

## 1.4. O que você vai conseguir fazer ao final do livro

- Criar um projeto Python testado do zero, com `pytest` e cobertura
- Aplicar **TDD** (Red → Green → Refactor) em uma feature real
- Configurar **lint** e **análise de segurança** automáticos
- Montar um pipeline de **CI** que roda a cada push e a cada PR
- Fazer **deploy automatizado** de uma página web no GitHub Pages
- Trabalhar em equipe usando **Pull Requests**, *templates* e *branch protection*

---

Próximo capítulo: [Capítulo 2 — Preparando o ambiente ➡](02-preparando-ambiente.md)
