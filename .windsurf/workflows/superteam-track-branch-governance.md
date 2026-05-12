---
description: Governar branches especiais, PRs incrementais, promocao para teste e standby de tracks Superteam
---
# Superteam Track Branch Governance

Use este workflow antes de criar, implementar, revisar, promover ou pausar qualquer track Superteam.

## Objetivo

Garantir que cada track tenha branch especial propria, PRs incrementais pequenos, criterios de garantia explicitos e uma decisao clara entre promover para `teste` ou manter em standby.

## Entradas obrigatorias

- Track alvo.
- Branch especial da track.
- Base de trabalho esperada, preferencialmente `origin/teste`.
- Repositorios afetados: raiz, `p2ploot-frontend`, `p2ploot-backend` ou combinacao.
- Tipo de mudanca: produto, demo, documentacao, integracao externa, auditoria ou analytics.

## Passos

1. Consultar regras ativas do projeto:

```text
/home/ci/Desktop/Projetos/P2PLOOT/regras/CONSULTA_REGRAS.md
```

2. Consultar o plano de governanca:

```text
/home/ci/Desktop/Projetos/P2PLOOT/docs/PLANO_GOVERNANCA_BRANCHES_TRACKS_SUPERTEAM.md
```

3. Verificar estado Git dos repositorios afetados:

```bash
git status --short --branch
```

Rodar no repositorio raiz e nos subrepositorios afetados.

4. Identificar alteracoes locais pre-existentes.

Registrar:

- arquivos modificados antes da tarefa;
- arquivos que devem ser evitados;
- arquivos que podem ser tocados;
- se existe submodule/gitlink modificado na raiz.

5. Confirmar o modelo de branch.

Branch especial:

```text
track/<slug-da-track>
```

Branch incremental:

```text
track-inc/<slug-da-track>/<numero>-<escopo-curto>
```

Observacao: nao usar `track/<slug-da-track>/inc-...`, porque Git nao permite criar uma ref abaixo de uma branch que ja existe como `track/<slug-da-track>`.

Target do PR incremental:

```text
track/<slug-da-track>
```

Target do PR de promocao:

```text
teste
```

6. Definir ou atualizar o charter da track.

O charter precisa conter:

- objetivo;
- MVP minimo;
- demo esperada;
- criterios de garantia;
- feature flags;
- evidencias esperadas;
- validacoes obrigatorias;
- riscos;
- condicao para standby.

7. Definir escopo do incremento.

Antes de editar, responder:

- o que sera alterado;
- o que ficara explicitamente fora;
- qual comportamento deve permanecer igual;
- quais contratos de API, props, rotas, status ou payloads precisam ser preservados;
- se ha impacto visual/UX;
- quais evidencias serao coletadas.

8. Aplicar workflows complementares quando necessario.

Frontend/UX:

```text
frontend-increment-preflight
frontend-visual-evidence-pack
frontend-review-bundle
```

Modularizacao frontend:

```text
frontend-extraction-playbook
```

9. Validar o incremento.

Validacoes comuns:

- `git diff --check`;
- build/testes do repo afetado;
- evidencias visuais para UX;
- logs, explorer links, dashboard ou screenshots quando a track depender disso;
- documentacao de limites e pendencias.

10. Decidir status apos o PR incremental.

Estados permitidos:

- `implementing`: ainda faltam incrementos.
- `ready-for-track-review`: MVP completo, precisa revisao final.
- `guaranteed`: passou nos gates da track.
- `promoted-to-teste`: PR da branch especial para `teste` foi aceito.
- `standby`: nao entra agora em `teste`, mas deve ficar preservada.

11. Para promocao para `teste`, exigir:

- branch especial atualizada contra `teste`;
- todos os gates aplicaveis atendidos;
- PR unico da branch especial para `teste`;
- feature flag documentada quando aplicavel;
- resumo de riscos e limitacoes;
- plano de rollback ou desativacao.

12. Para standby, exigir:

- branch especial enviada para remoto;
- status `standby` registrado;
- ultimo commit bom identificado;
- pendencias e proximo passo documentados;
- nenhum merge parcial em `teste`.

## Criterios de pronto

- Branch alvo correta confirmada.
- PR incremental aponta para a branch especial da track.
- PR de promocao aponta para `teste` apenas se a track estiver garantida.
- Alteracoes locais de terceiros foram protegidas.
- Validacoes e evidencias estao registradas.
- Status final da track esta explicito.
