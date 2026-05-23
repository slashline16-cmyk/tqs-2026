# Capítulo 11 — Recursos & Licença

[⬅ Anterior: Atividade prática](10-atividade-pratica.md) · [Sumário](../README.md)

---

Você chegou ao fim do livro. Este capítulo é a **bibliografia** — onde buscar mais quando precisar — e a licença.

## 11.1. Documentação oficial

Sempre que tiver dúvida sobre alguma ferramenta, **a documentação oficial é a primeira parada** (antes de tutoriais e blogposts):

- [pytest](https://docs.pytest.org/) — framework de testes
- [Flask](https://flask.palletsprojects.com/) — framework web
- [ruff](https://docs.astral.sh/ruff/) — lint + formatação
- [bandit](https://bandit.readthedocs.io/) — análise de segurança
- [GitHub Actions](https://docs.github.com/actions) — CI/CD
- [GitHub Pages](https://docs.github.com/pages) — hospedagem estática

## 11.2. Bibliografia da disciplina

Livros clássicos referenciados na ementa:

- Myers, G. J. *The Art of Software Testing*. Wiley.
- Delamaro, M. E.; Maldonado, J. C.; Jino, M. *Introdução ao Teste de Software*. Elsevier.
- Beck, K. *Test Driven Development: By Example*. Addison-Wesley.
- Crispin, L.; Gregory, J. *Agile Testing: A Practical Guide for Testers and Agile Teams*. Addison-Wesley.

## 11.3. Para ir além

Tópicos que **não** entraram neste projeto para mantê-lo simples, mas que são o próximo passo natural:

| Tema | Ferramentas | Por quê |
|---|---|---|
| **Testes E2E de UI** | [Selenium](https://www.selenium.dev/), [Playwright](https://playwright.dev/), [Cypress](https://www.cypress.io/) | Testar a interface inteira no navegador, simulando usuário real |
| **Dashboards de qualidade** | [Codecov](https://about.codecov.io/), [SonarCloud](https://sonarcloud.io/) | Histórico visual de cobertura, code smells, dívida técnica |
| **Mutation testing** | [`mutmut`](https://mutmut.readthedocs.io/), [`cosmic-ray`](https://cosmic-ray.readthedocs.io/) | Testar se seus **testes** são bons (não só se cobrem o código) |
| **Pre-commit hooks** | [pre-commit](https://pre-commit.com/) | Roda lint/format **antes** de cada commit local, sem você lembrar |
| **Type checking** | [mypy](https://mypy.readthedocs.io/), [pyright](https://microsoft.github.io/pyright/) | Pega erros de tipo em tempo de análise, sem rodar o código |
| **Performance testing** | [Locust](https://locust.io/), [k6](https://k6.io/) | Mede como a aplicação se comporta sob carga |
| **Security testing** | [OWASP ZAP](https://www.zaproxy.org/), [Burp Suite](https://portswigger.net/burp) | Testes ativos de segurança (vai muito além do `bandit`) |
| **Contract testing** | [Pact](https://pact.io/) | Garante que serviços (ou linguagens espelhadas) concordam num contrato |
| **BDD** | [behave](https://behave.readthedocs.io/), [pytest-bdd](https://pytest-bdd.readthedocs.io/) | Gherkin (Given-When-Then), aproxima testes da linguagem do negócio |

## 11.4. Padrões e processos

Conceitos úteis para acompanhar a leitura:

- **Pirâmide de testes** (Cohn) — muitos unitários, alguns de integração, poucos E2E
- **Test smells** — anti-padrões em código de teste (Bowes et al.)
- **ISO/IEC 25010** — modelo de qualidade de produto de software (visto na Aula 04)
- **CMMI**, **MPS.BR** — qualidade do processo (visto na Aula 05)

## 11.5. Comunidades

Onde tirar dúvidas reais (em ordem de qualidade de resposta):

- **Documentação oficial** — quase sempre tem o que você precisa
- **Stack Overflow** com a tag da ferramenta — cuidado com respostas antigas
- **GitHub Discussions** ou **Issues** do próprio projeto
- Discords/Slacks oficiais (pytest, Flask, etc.)

> **Anti-padrão**: confiar cegamente em respostas de IA generativa para detalhes de versão. Sempre **confirme** com a documentação oficial — a IA pode estar olhando para uma versão antiga ou misturando APIs.

## 11.6. Licença

Este projeto está sob a [Licença MIT](../LICENSE). Você pode usar, modificar e distribuir livremente, inclusive em sala de aula, com fins comerciais ou não, desde que mantenha a nota de copyright.

---

## Encerramento

Se você chegou até aqui rodando cada exemplo, conferindo cada arquivo e fazendo a [atividade prática](10-atividade-pratica.md), você tem agora uma base sólida em:

- Testes automatizados em Python (pytest)
- Desenvolvimento guiado por testes (TDD)
- Qualidade de código (lint, format, segurança, cobertura)
- Integração contínua (GitHub Actions)
- Deploy automatizado (GitHub Pages)
- Colaboração com Pull Requests, *branch protection*, code review

Esses são os **fundamentos** — eles se transferem para qualquer linguagem, framework ou empresa.

**Boa sorte na disciplina e na carreira!** 🎓

— Prof. Helvecio Bezerra Leal Neto
   Disciplina **PC010027 — Teste e Qualidade de Software**
   Universidade Federal do Oeste do Pará (UFOPA)
   2026

---

[⬅ Voltar ao Sumário](../README.md)
