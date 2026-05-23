# Capítulo 10 — Atividade prática

[⬅ Anterior: Pull Requests](09-pull-requests.md) · [Sumário](../README.md) · Próximo: [Capítulo 11 — Recursos & Licença ➡](11-recursos-e-licenca.md)

---

Você leu os capítulos anteriores — agora é hora de **fazer**. Este capítulo é um **passo a passo executável**: cada tarefa traz o **código pronto** para você copiar, os **comandos exatos** para rodar e o **corpo do PR já preenchido**.

> **Por que dou o código pronto?** O objetivo desta atividade **não é** você reinventar o algoritmo do CNPJ. O objetivo é praticar o **workflow do Pull Request**: criar branch, commit a cada passo do TDD, abrir PR com template, ver o CI, fazer code review. Esse é o conjunto de habilidades que se transfere para qualquer projeto na sua carreira.

> 💻 **Todos os comandos deste capítulo rodam no terminal do Codespace** que você abriu no [capítulo 2](02-preparando-ambiente.md). Se ainda não tem o Codespace aberto, volte e abra antes de seguir.

## Roteiro do capítulo

| Seção | O que é | Quem faz |
|---|---|---|
| **10.1 Demonstração guiada** — PR do "trim" | Modelo de PR que o professor executa ao vivo em aula | 👨‍🏫 Professor |
| **10.2 Tarefa 1** — Validador de CNPJ | PR com código pronto | 👨‍🎓 Aluno |
| **10.3 Tarefa 2** — Validador de telefone | PR com código pronto | 👨‍🎓 Aluno |
| **10.4 Tarefa 3** — Quebrar o CI (e consertar) | Atividade exploratória | 👨‍🎓 Aluno |
| **10.5 Tarefa 4** — Branch protection | Atividade exploratória | 👨‍🎓 Aluno |
| **10.6 Tarefa 5** — Mutation testing (bônus) | Atividade exploratória | 👨‍🎓 Aluno |
| **10.7 Como entregar** | Critérios de avaliação | — |

---

## 10.1. Demonstração guiada — PR do "trim" (modelo)

> **Para o aluno**: o professor executa esta seção ao vivo em aula. Acompanhe pela tela. Você não precisa replicar este PR — ele é só o modelo do formato que as Tarefas 1, 2 e 3 vão seguir.

**Cenário**: usuários costumam copiar CPF de um PDF ou e-mail de uma planilha e vem espaço em branco nas pontas. A validação rejeita sem motivo aparente. Vamos consertar.

### Passo 1 — Criar a branch

```bash
git checkout main
git pull
git checkout -b feat/aceitar-espacos-nas-pontas
```

### Passo 2 — RED: adicionar testes que falham

Abra [`tests/test_validators.py`](../tests/test_validators.py) e **acrescente no final** (mantenha os 4 testes existentes):

```python
def test_aceita_cpf_com_espacos_nas_pontas():
    assert validar_cpf("  111.444.777-35  ") is True


def test_aceita_email_com_espacos_nas_pontas():
    assert validar_email("  aluno@ufopa.edu.br  ") is True
```

Rode:

```bash
pytest
```

Você verá **2 testes falhando**. Isso é esperado — eles testam um comportamento que ainda não existe.

```bash
git add tests/test_validators.py
git commit -m "test: adiciona testes para CPF/email com espaços nas pontas"
```

### Passo 3 — GREEN: implementação mínima

Edite [`src/validators.py`](../src/validators.py). Adicione `.strip()` em dois lugares:

```python
def validar_cpf(cpf: str | None) -> bool:
    if not isinstance(cpf, str):
        return False
    cpf = cpf.strip()                              # <-- NOVO
    apenas_digitos = re.sub(r"[.\-\s]", "", cpf)
    # ... resto inalterado
```

```python
def validar_email(email: str | None) -> bool:
    if not isinstance(email, str) or not email:
        return False
    return _REGEX_EMAIL.match(email.strip()) is not None   # .strip() novo
```

Rode `pytest` → **6 testes passando**.

```bash
git add src/validators.py
git commit -m "feat: aceita CPF e email com espaços nas pontas"
```

### Passo 4 — Espelhar no JS

Edite [`docs/validators.js`](../docs/validators.js) e adicione `.trim()` nos dois validadores:

```javascript
export function validarCpf(cpf) {
    if (typeof cpf !== "string") return false;
    cpf = cpf.trim();                              // <-- NOVO
    const apenasDigitos = cpf.replace(/[.\-\s]/g, "");
    // ... resto inalterado
}

export function validarEmail(email) {
    if (typeof email !== "string" || email.length === 0) return false;
    return REGEX_EMAIL.test(email.trim());         // .trim() novo
}
```

```bash
git add docs/validators.js
git commit -m "chore: espelha trim no validators.js"
```

### Passo 5 — Push e abertura do PR

```bash
git push -u origin feat/aceitar-espacos-nas-pontas
```

No GitHub, clique em **"Compare & pull request"**. O template aparece automaticamente. Preencha:

```markdown
## Descrição

Atualmente, se um usuário copia o CPF de um documento PDF ou cola um e-mail
de uma planilha, vem espaço em branco nas pontas e a validação falha
silenciosamente. Esta mudança aplica `.strip()` (Python) e `.trim()` (JS)
antes da validação para resolver esse caso comum.

## Tipo de mudança

- [x] Nova funcionalidade

## Checklist de qualidade

- [x] Os testes existentes continuam passando (`pytest`)
- [x] Adicionei testes para a nova funcionalidade (2 novos)
- [x] A cobertura de testes está em ≥ 80%
- [x] O lint passa sem erros (`ruff check . && ruff format --check .`)
- [x] A análise de segurança passa (`bandit -r src/`)

## Como testar

1. `git checkout feat/aceitar-espacos-nas-pontas`
2. Rode `pytest` — 6 testes passando (eram 4, adicionados 2)
3. Suba o Flask: `flask --app src.app run`
4. Acesse <http://localhost:5000>
5. Digite `  111.444.777-35  ` (com espaços) — deve aceitar como válido

## Issue relacionada

Nenhuma — descoberto durante uso em sala.
```

### Passo 6 — Conferir o CI e mergear

Aguarde o CI rodar (~1 minuto). Você verá ✅ na aba do PR. Clique em **Squash and merge** → **Confirm**. Apague a branch.

🎉 Pronto. Esse foi o ciclo completo. Agora você faz o mesmo nas tarefas a seguir.

---

## 10.2. Tarefa 1 — Validador de CNPJ

**Branch**: `feat/validar-cnpj`

### Passo 1 — Criar a branch

```bash
git checkout main && git pull
git checkout -b feat/validar-cnpj
```

### Passo 2 — RED: adicionar testes

Em [`tests/test_validators.py`](../tests/test_validators.py), **adicione no topo do arquivo** o import de `validar_cnpj` (que ainda não existe — é proposital):

```python
from src.validators import validar_cpf, validar_email, validar_cnpj
```

E **no final do arquivo**, adicione:

```python
def test_aceita_cnpj_valido_com_mascara():
    assert validar_cnpj("11.222.333/0001-81") is True


def test_aceita_cnpj_valido_sem_mascara():
    assert validar_cnpj("11222333000181") is True


def test_rejeita_cnpj_com_digito_verificador_errado():
    assert validar_cnpj("11.222.333/0001-82") is False


def test_rejeita_cnpj_com_todos_digitos_iguais():
    assert validar_cnpj("11111111111111") is False


def test_rejeita_cnpj_vazio():
    assert validar_cnpj("") is False
```

Rode `pytest` → você verá `ImportError: cannot import name 'validar_cnpj'`. **Esperado.**

```bash
git add tests/test_validators.py
git commit -m "test: adiciona testes para validar_cnpj"
```

### Passo 3 — GREEN: implementação

Em [`src/validators.py`](../src/validators.py), **adicione no final do arquivo**:

```python
def _calcular_dv_cnpj(digitos: str, pesos: list[int]) -> int:
    soma = sum(int(d) * p for d, p in zip(digitos, pesos, strict=True))
    resto = soma % 11
    return 0 if resto < 2 else 11 - resto


def validar_cnpj(cnpj: str | None) -> bool:
    if not isinstance(cnpj, str):
        return False
    apenas_digitos = re.sub(r"[.\-/\s]", "", cnpj.strip())
    if len(apenas_digitos) != 14 or not apenas_digitos.isdigit():
        return False
    if len(set(apenas_digitos)) == 1:
        return False
    pesos_primeiro = [5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2]
    pesos_segundo = [6, 5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2]
    primeiro = _calcular_dv_cnpj(apenas_digitos[:12], pesos_primeiro)
    segundo = _calcular_dv_cnpj(apenas_digitos[:13], pesos_segundo)
    return apenas_digitos[12] == str(primeiro) and apenas_digitos[13] == str(segundo)
```

Rode `pytest` → todos os testes passam.

```bash
git add src/validators.py
git commit -m "feat: adiciona validar_cnpj"
```

### Passo 4 — Espelhar no JS

Em [`docs/validators.js`](../docs/validators.js), **adicione no final**:

```javascript
function calcularDvCnpj(digitos, pesos) {
    let soma = 0;
    for (let i = 0; i < digitos.length; i++) {
        soma += parseInt(digitos[i], 10) * pesos[i];
    }
    const resto = soma % 11;
    return resto < 2 ? 0 : 11 - resto;
}

export function validarCnpj(cnpj) {
    if (typeof cnpj !== "string") return false;
    const apenasDigitos = cnpj.trim().replace(/[.\-/\s]/g, "");
    if (apenasDigitos.length !== 14 || !/^\d{14}$/.test(apenasDigitos)) return false;
    if (new Set(apenasDigitos).size === 1) return false;
    const pesosP = [5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2];
    const pesosS = [6, 5, 4, 3, 2, 9, 8, 7, 6, 5, 4, 3, 2];
    const primeiro = calcularDvCnpj(apenasDigitos.slice(0, 12), pesosP);
    const segundo = calcularDvCnpj(apenasDigitos.slice(0, 13), pesosS);
    return apenasDigitos[12] === String(primeiro) && apenasDigitos[13] === String(segundo);
}
```

```bash
git add docs/validators.js
git commit -m "chore: espelha validar_cnpj no validators.js"
```

### Passo 5 — Push e PR

```bash
git push -u origin feat/validar-cnpj
```

Abra o PR no GitHub. Preencha o template:

```markdown
## Descrição

Adiciona a função `validar_cnpj` em `src/validators.py`, com cálculo dos
dois dígitos verificadores conforme algoritmo oficial. Espelhada em
`docs/validators.js` para o demo do GitHub Pages.

## Tipo de mudança

- [x] Nova funcionalidade

## Checklist de qualidade

- [x] Os testes existentes continuam passando
- [x] Adicionei 5 testes novos cobrindo: válido com máscara, válido sem
      máscara, dígito verificador errado, todos dígitos iguais, vazio
- [x] Cobertura ≥ 80%
- [x] Lint passa (`ruff check .`)
- [x] Bandit passa

## Como testar

1. `git checkout feat/validar-cnpj`
2. `pytest` → 11 testes passando (6 antigos + 5 novos)
3. No Python: `validar_cnpj("11.222.333/0001-81")` → `True`
```

Aguarde CI verde, faça squash and merge.

---

## 10.3. Tarefa 2 — Validador de telefone celular

**Branch**: `feat/validar-telefone`

**Especificação**: aceita formato `(99) 99999-9999` ou `99999999999` (11 dígitos), DDD entre 11 e 99, e o **nono dígito** (logo após o DDD) precisa ser `9`.

### Passo 1 — Criar a branch

```bash
git checkout main && git pull
git checkout -b feat/validar-telefone
```

### Passo 2 — RED: testes unitários

Em [`tests/test_validators.py`](../tests/test_validators.py), atualize o import:

```python
from src.validators import validar_cpf, validar_email, validar_cnpj, validar_telefone
```

E adicione no final:

```python
def test_aceita_telefone_valido_com_mascara():
    assert validar_telefone("(11) 91234-5678") is True


def test_aceita_telefone_valido_sem_mascara():
    assert validar_telefone("11912345678") is True


def test_rejeita_telefone_sem_nono_digito():
    assert validar_telefone("(11) 81234-5678") is False


def test_rejeita_telefone_com_ddd_invalido():
    assert validar_telefone("(10) 91234-5678") is False


def test_rejeita_telefone_com_tamanho_errado():
    assert validar_telefone("1191234567") is False
```

Rode `pytest` → ImportError esperado.

```bash
git add tests/test_validators.py
git commit -m "test: adiciona testes para validar_telefone"
```

### Passo 3 — GREEN: implementação

Em [`src/validators.py`](../src/validators.py), no final:

```python
def validar_telefone(telefone: str | None) -> bool:
    if not isinstance(telefone, str):
        return False
    apenas_digitos = re.sub(r"[()\-\s]", "", telefone.strip())
    if len(apenas_digitos) != 11 or not apenas_digitos.isdigit():
        return False
    ddd = int(apenas_digitos[:2])
    if not (11 <= ddd <= 99):
        return False
    return apenas_digitos[2] == "9"
```

Rode `pytest` → todos passam.

```bash
git add src/validators.py
git commit -m "feat: adiciona validar_telefone"
```

### Passo 4 — Espelhar no JS

Em [`docs/validators.js`](../docs/validators.js), no final:

```javascript
export function validarTelefone(telefone) {
    if (typeof telefone !== "string") return false;
    const apenasDigitos = telefone.trim().replace(/[()\-\s]/g, "");
    if (apenasDigitos.length !== 11 || !/^\d{11}$/.test(apenasDigitos)) return false;
    const ddd = parseInt(apenasDigitos.slice(0, 2), 10);
    if (ddd < 11 || ddd > 99) return false;
    return apenasDigitos[2] === "9";
}
```

```bash
git add docs/validators.js
git commit -m "chore: espelha validar_telefone no validators.js"
```

### Passo 5 — Atualizar o formulário (Flask e estático)

Em [`src/templates/index.html`](../src/templates/index.html), **adicione o campo de telefone** logo abaixo do campo de e-mail (dentro do `<form>`):

```html
<div>
    <label for="telefone">Telefone</label>
    <input type="text" id="telefone" name="telefone" placeholder="(11) 91234-5678" required>
</div>
```

E no `<script>`, atualize o handler para enviar e exibir telefone:

```javascript
document.getElementById("formulario").addEventListener("submit", async (e) => {
    e.preventDefault();
    const cpf = document.getElementById("cpf").value;
    const email = document.getElementById("email").value;
    const telefone = document.getElementById("telefone").value;
    const resposta = await fetch("/validar", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ cpf, email, telefone }),
    });
    const dados = await resposta.json();
    const div = document.getElementById("resultado");
    div.innerHTML = `
        <div class="resultado ${dados.cpf_valido ? "valido" : "invalido"}">
            CPF: ${dados.cpf_valido ? "válido" : "inválido"}
        </div>
        <div class="resultado ${dados.email_valido ? "valido" : "invalido"}">
            E-mail: ${dados.email_valido ? "válido" : "inválido"}
        </div>
        <div class="resultado ${dados.telefone_valido ? "valido" : "invalido"}">
            Telefone: ${dados.telefone_valido ? "válido" : "inválido"}
        </div>
    `;
});
```

Em [`docs/index.html`](../docs/index.html), faça o mesmo: adicione o campo de telefone no `<form>` e atualize o `<script>` (importando `validarTelefone` no `import`):

```javascript
import { validarCpf, validarEmail, validarTelefone } from "./validators.js";

document.getElementById("formulario").addEventListener("submit", (e) => {
    e.preventDefault();
    const cpf = document.getElementById("cpf").value;
    const email = document.getElementById("email").value;
    const telefone = document.getElementById("telefone").value;
    const cpfValido = validarCpf(cpf);
    const emailValido = validarEmail(email);
    const telefoneValido = validarTelefone(telefone);
    const div = document.getElementById("resultado");
    div.innerHTML = `
        <div class="resultado ${cpfValido ? "valido" : "invalido"}">
            CPF: ${cpfValido ? "válido" : "inválido"}
        </div>
        <div class="resultado ${emailValido ? "valido" : "invalido"}">
            E-mail: ${emailValido ? "válido" : "inválido"}
        </div>
        <div class="resultado ${telefoneValido ? "valido" : "invalido"}">
            Telefone: ${telefoneValido ? "válido" : "inválido"}
        </div>
    `;
});
```

### Passo 6 — Atualizar a rota Flask

Em [`src/app.py`](../src/app.py), na função `validar`:

```python
@app.post("/validar")
def validar():
    dados = request.get_json(silent=True) or request.form
    cpf = dados.get("cpf", "")
    email = dados.get("email", "")
    telefone = dados.get("telefone", "")
    return jsonify(
        cpf_valido=validar_cpf(cpf),
        email_valido=validar_email(email),
        telefone_valido=validar_telefone(telefone),
    )
```

Lembre-se de adicionar `validar_telefone` no import do topo:

```python
from src.validators import validar_cpf, validar_email, validar_telefone
```

### Passo 7 — Teste de integração

Em [`tests/test_app.py`](../tests/test_app.py), atualize o teste existente para incluir telefone:

```python
def test_validar_retorna_resultados_corretos(client):
    resposta = client.post(
        "/validar",
        json={
            "cpf": "111.444.777-35",
            "email": "aluno@ufopa.edu.br",
            "telefone": "(11) 91234-5678",
        },
    )
    assert resposta.get_json() == {
        "cpf_valido": True,
        "email_valido": True,
        "telefone_valido": True,
    }
```

Rode `pytest` para garantir que tudo passa.

```bash
git add src/ docs/ tests/
git commit -m "feat: integra validar_telefone no formulário e na rota Flask"
```

### Passo 8 — Push e PR

```bash
git push -u origin feat/validar-telefone
```

Abra o PR seguindo o mesmo padrão da Tarefa 1.

---

## 10.4. Tarefa 3 — Quebrar o CI (e consertar)

**Objetivo**: passar pela experiência de ver o CI ficar vermelho num ambiente controlado, **antes** que aconteça num projeto real.

**Branch**: `chore/quebrando-ci`

### Passos

```bash
git checkout main && git pull
git checkout -b chore/quebrando-ci
```

1. Abra [`src/validators.py`](../src/validators.py) e **apague um caractere** — por exemplo, remova o `:` no final da assinatura de uma função:

   ```python
   def validar_cpf(cpf: str | None) -> bool      # falta o : no final
   ```

2. Salve, commit, push:

   ```bash
   git add src/validators.py
   git commit -m "chore: introduz erro de sintaxe (proposital, exercício)"
   git push -u origin chore/quebrando-ci
   ```

3. Abra o PR. Aguarde o CI rodar.

4. Veja o ✗ vermelho no PR. Clique nele → vai para a aba **Actions**.

5. Clique no workflow → no job → no passo vermelho. **Leia o log de baixo para cima**.

6. Identifique o erro. Conserte localmente (recoloque o `:`), commit, push.

7. Observe o CI ficar verde no PR.

8. **Não mergeie esse PR** — feche sem merge (botão **Close pull request** no final da página).

### Reflexão (escreva como comentário no PR antes de fechar)

- Qual passo do pipeline falhou primeiro?
- Os outros passos chegaram a rodar?
- Em quanto tempo o CI te avisou?
- Quanto tempo levaria se você dependesse de outro desenvolvedor rodar manualmente?

---

## 10.5. Tarefa 4 — Branch protection

**Objetivo**: experimentar a sensação de não conseguir burlar o processo.

### Passos

1. Ative branch protection na `main` seguindo o capítulo [9.2](09-pull-requests.md#92-ativando-branch-protection-na-main).
2. Tente fazer um commit + `git push origin main` **direto**, com mudança trivial.
3. Anote o erro que o GitHub retorna.
4. Faça a mesma mudança via PR → veja funcionar.
5. Responda em texto: **que classe de erros essa proteção impede que aconteça?**

> Não há código nesta tarefa — é configuração de repo + reflexão.

---

## 10.6. Tarefa 5 (bônus) — Mutation testing

**Objetivo**: descobrir que cobertura 100% não significa testes bons.

### Passos

```bash
pip install mutmut
mutmut run --paths-to-mutate src/validators.py
mutmut results
```

O `mutmut` modifica seu código (troca `==` por `!=`, `+` por `-`, etc.) e checa se algum teste pega cada mutação. Se nenhum teste pegar uma mutação, seu teste é fraco para aquele caso.

### Para entregar

- Anexe a saída de `mutmut results` ao seu relatório.
- Discuta: **quantas mutações sobreviveram?** Para cada uma, qual teste você adicionaria para matá-la?

---

## 10.7. Como entregar

Cada tarefa de PR deve virar **um PR separado** no seu fork (ou repositório pessoal):

| Tarefa | Entrega |
|---|---|
| 1 (CNPJ) | Link do PR mergeado, CI verde |
| 2 (Telefone) | Link do PR mergeado, CI verde |
| 3 (Quebrar CI) | Link do PR fechado sem merge + comentário com reflexão |
| 4 (Branch protection) | Print do erro do `git push` + reflexão em texto |
| 5 (Mutation testing) | Saída de `mutmut results` + análise |

### Checklist final para cada PR

- [ ] Branch nomeada conforme convenção (`feat/`, `chore/`, etc.)
- [ ] Commits separados por passo (test → feat → chore se aplicável)
- [ ] PR seguindo o template
- [ ] CI verde
- [ ] Pelo menos uma auto-revisão (comentários do próprio autor explicando decisões)

---

Próximo capítulo: [Capítulo 11 — Recursos & Licença ➡](11-recursos-e-licenca.md)
