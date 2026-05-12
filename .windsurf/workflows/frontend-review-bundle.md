---
description: Preparar pacote final de revisão frontend com diff, validação, contratos e evidências
---
# Frontend Review Bundle

Use este workflow ao concluir um incremento frontend ou antes de abrir/atualizar PR.

## Objetivo

Entregar para revisão humana um pacote conciso, auditável e acionável com o que mudou, como validar, quais contratos foram preservados e onde olhar primeiro.

## Quando usar

Use depois de:

- modularização frontend;
- refatoração de componente/hook/util;
- correção visual/UX;
- mudança em modal, formulário, página ou fluxo;
- preparação de commit ou PR.

## Passos

1. Verificar status do repositório raiz:

```bash
git status --short
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT
```

2. Verificar status do frontend:

```bash
git status --short && git branch --show-current
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-frontend
```

3. Gerar resumo do diff do frontend:

```bash
git diff --stat
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-frontend
```

4. Listar arquivos alterados:

```bash
git diff --name-status
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-frontend
```

Se o incremento estiver commitado, comparar contra a base correta, geralmente:

```bash
git diff --stat origin/teste...HEAD && git diff --name-status origin/teste...HEAD
```

5. Rodar validação de whitespace/diff:

```bash
git diff --check
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-frontend
```

6. Rodar build obrigatório do frontend:

```bash
VITE_API_URL=http://localhost:6110 npm run build
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-frontend
```

7. Registrar avisos conhecidos separadamente.

Exemplo:

- avisos Rollup sobre anotações `/*#__PURE__*/` em dependências de terceiros;
- `npm run lint` indisponível por configuração ESLint ausente, se ainda for o caso;
- `dist` apareceu no workspace após build, mas não será incluído no commit.

8. Conferir contratos preservados.

Listar explicitamente:

- chamadas `api.*` preservadas;
- payloads preservados;
- props públicas preservadas;
- exports/rotas preservados;
- ausência de mudança no backend.

9. Conferir evidências visuais quando aplicável.

Verificar se existem:

- antes desktop;
- depois desktop;
- antes mobile;
- depois mobile;
- estados adicionais relevantes.

Caminho padrão:

```text
docs/evidencias/frontend/incremento-N-nome-curto/
```

10. Atualizar documentação incremental quando o trabalho fizer parte da modularização frontend:

```text
/home/ci/Desktop/Projetos/P2PLOOT/docs/MODULARIZACAO_INCREMENTAL_FRONTEND.md
```

A seção deve incluir:

- resumo;
- arquivos criados/modificados;
- validação executada;
- contratos preservados;
- evidências visuais;
- próximo incremento recomendado, se aplicável.

11. Preparar mensagem de commit usando Conventional Commits.

Exemplos:

```text
refactor(web3games): extract game catalog filters
refactor(web3games): split game comments section
fix(market): improve seller chat layout
docs: update frontend modularization evidence
```

12. Preparar resumo de PR.

Template recomendado:

```md
## Resumo

- ...
- ...

## Arquivos principais

- `arquivo`: motivo
- `arquivo`: motivo

## Contratos preservados

- ...

## Validação

- [x] `git diff --check`
- [x] `VITE_API_URL=http://localhost:6110 npm run build`

## Evidências visuais

- Desktop antes/depois: `...`
- Mobile antes/depois: `...`
- Resultado visual: preservado / mudança intencional

## Como revisar

1. Começar por `...`
2. Revisar `...`
3. Conferir evidências em `...`

## Observações

- ...
```

## Ordem recomendada para revisão humana

1. Ler o resumo e a fronteira do incremento.
2. Revisar hooks/utils puros primeiro.
3. Revisar componentes extraídos.
4. Revisar shell/arquivo original.
5. Conferir contratos de API/props.
6. Conferir prints antes/depois.
7. Conferir build e status Git.

## Critérios de pronto para PR

- `git diff --check` passou.
- Build passou com `VITE_API_URL=http://localhost:6110`.
- Arquivos fora do escopo foram evitados ou justificados.
- Contratos de API/props/exports foram preservados ou mudança foi explicitamente aprovada.
- Evidências visuais estão documentadas quando aplicável.
- `docs/MODULARIZACAO_INCREMENTAL_FRONTEND.md` foi atualizado quando necessário.
- Commit segue Conventional Commits.
- PR aponta para a branch correta, geralmente `teste` no frontend.
