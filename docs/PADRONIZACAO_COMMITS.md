# Padrão de Commits

Este projeto utiliza um padrão baseado no Conventional Commits para manter o histórico organizado e facilitar a leitura das alterações.

## Formato do commit

tipo: descrição

Exemplo:
```
feat: adiciona suporte a múltiplos usuários
```

---

## Tipos de commit

### feat
Nova funcionalidade adicionada ao sistema.

Exemplo:
```
feat: adiciona autenticação com JWT
```

### fix
Correção de bug.

Exemplo:
```
fix: corrige erro no cálculo da média
```

### docs
Alterações na documentação.

Exemplo:
```
docs: atualiza instruções de instalação
```

### style
Mudanças apenas visuais ou de formatação (não altera lógica).

Exemplo:
```
style: ajusta indentação no controller
```

### refactor
Refatoração de código sem alterar comportamento.

Exemplo:
```
refactor: simplifica validação de usuário
```

### test
Adição ou modificação de testes.

Exemplo:
```
test: adiciona testes para UserService
```

### chore
Tarefas de manutenção.

Exemplo:
```
chore: atualiza dependências do projeto
```

---

## Boas práticas

- Use descrição curta e objetiva
- Escreva a descrição usando verbos no presente (ex: "adicionar", "corrigir", "remover")
- Evite commits genéricos como:
  - "update"
  - "mudanças"
  - "ajustes"

---

## Exemplo de commit completo

```bash
git commit -m "feat: implementa endpoint de cadastro de usuário"

