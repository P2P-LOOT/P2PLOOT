# Consulta de Regras e Instruções do Projeto

Este arquivo centraliza regras operacionais, preferências e instruções novas que devem ser consultadas antes de iniciar incrementos no P2PLOOT.

A intenção é manter um ponto único de referência para decisões recorrentes, validação, auditoria e continuidade entre sessões.

## Como usar

Antes de iniciar uma nova tarefa ou incremento:

1. Ler este arquivo.
2. Verificar se a tarefa toca frontend, backend, UX, documentação, PRs ou submodules.
3. Aplicar as regras registradas aqui junto com a documentação específica do incremento.
4. Quando a usuária der uma nova instrução recorrente, registrar uma nova entrada neste arquivo.

## Regras ativas

### 1. Incrementos frontend/UX devem ter evidência visual

Toda alteração que afete frontend, visualização, layout, interação ou UX deve registrar prints para validação e auditoria posterior.

Obrigatório registrar:

- Print do estado **antes** em desktop.
- Print do estado **depois** em desktop.
- Print do estado **antes** em mobile.
- Print do estado **depois** em mobile.
- Mesma rota, usuário/perfil, dados e viewport equivalente sempre que possível.
- As evidências devem dar transparência para as duas versões: **desktop** e **mobile**.
- Se alguma versão não puder ser capturada, registrar explicitamente o motivo na documentação do incremento.
- Estados adicionais quando aplicável:
  - modal aberto;
  - loading;
  - erro;
  - sucesso;
  - empty state;
  - fluxo intermediário relevante.

Caminho recomendado:

```text
docs/evidencias/frontend/incremento-N-nome-curto/
```

Exemplo:

```text
docs/evidencias/frontend/incremento-3-add-edit-game-form/antes-desktop.png
docs/evidencias/frontend/incremento-3-add-edit-game-form/depois-desktop.png
docs/evidencias/frontend/incremento-3-add-edit-game-form/antes-mobile.png
docs/evidencias/frontend/incremento-3-add-edit-game-form/depois-mobile.png
```

A documentação do incremento deve indicar se o resultado visual foi:

- preservado sem mudança visual esperada; ou
- alterado intencionalmente, explicando o que mudou, por que mudou e qual impacto esperado.

### 2. Incrementos devem continuar pequenos e validáveis

Cada incremento deve ter escopo claro, com fronteira pequena e validação independente.

Priorizar:

- uma área por vez;
- build após bloco relevante;
- commits estratégicos;
- documentação atualizada ao final;
- PR com resumo claro de escopo e validação.

### 3. Preservar contratos e evitar backend sem necessidade

Durante modularizações frontend:

- não alterar backend sem pedido explícito;
- preservar contratos de API existentes;
- preservar props públicas de componentes quando possível;
- usar arquivos de compatibilidade temporários quando necessário.

### 4. Proteger alterações locais e escopo do PR

Antes de mexer em arquivos:

- rodar `git status --short` no repositório relevante;
- identificar arquivos modificados por outra pessoa;
- evitar tocar arquivos fora do escopo;
- não misturar mudanças não relacionadas no mesmo commit ou PR.

### 5. Build frontend exige `VITE_API_URL`

Para validar build do frontend, usar:

```bash
VITE_API_URL=http://localhost:6110 npm run build
```

O build simples com `npm run build` pode falhar se `VITE_API_URL` não estiver definido.

### 6. Commits devem seguir Conventional Commits

Usar mensagens como:

```text
feat(scope): descricao
refactor(scope): descricao
fix(scope): descricao
docs: descricao
chore: descricao
```

Exemplos usados no projeto:

```text
refactor(web3games): extract edit game form components
refactor(web3games): extract edit game form hook
docs: update frontend modularization increment 2
```

### 7. Documentação incremental deve ser atualizada

Ao concluir incremento de modularização frontend, atualizar:

```text
MODULARIZACAO_INCREMENTAL_FRONTEND.md
```

A atualização deve incluir:

- resumo;
- arquivos criados/modificados;
- commits relevantes;
- validação executada;
- contratos preservados;
- evidências visuais quando houver impacto frontend/UX.

## Template para nova regra

```md
### N. Título da regra

Data:
Origem:
Escopo:

Regra:

- ...

Como aplicar:

- ...

Exceções:

- ...
```
