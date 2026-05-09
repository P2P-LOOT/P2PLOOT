---
description: Preparar incremento frontend com checagem de regras, escopo, riscos e validação
---
# Frontend Increment Preflight

Use este workflow antes de iniciar qualquer incremento de frontend, modularização, UX, layout, modal, fluxo visual ou PR relacionado ao `p2ploot-frontend`.

## Objetivo

Garantir que o agente comece com contexto suficiente, escopo pequeno, proteção de alterações locais e critérios claros de validação/revisão.

## Passos

1. Consultar regras ativas do projeto:

```text
/home/ci/Desktop/Projetos/P2PLOOT/regras/CONSULTA_REGRAS.md
```

2. Consultar o plano incremental quando a tarefa envolver modularização frontend:

```text
/home/ci/Desktop/Projetos/P2PLOOT/MODULARIZACAO_INCREMENTAL_FRONTEND.md
```

3. Verificar o estado do repositório raiz:

```bash
git status --short
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT
```

4. Verificar o estado do frontend:

```bash
git status --short && git branch --show-current
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-frontend
```

5. Identificar alterações locais que não pertencem ao incremento atual.

Obrigatório registrar:

- arquivos modificados previamente;
- arquivos que devem ser evitados;
- arquivos que podem ser tocados;
- se o submodule/gitlink aparece modificado na raiz.

6. Confirmar a base de trabalho.

Padrão recomendado para novos incrementos:

```text
origin/teste
```

Exceção: continuar uma branch já aprovada pela usuária ou explicitamente necessária para o incremento.

7. Definir a fronteira do incremento.

Responder antes de editar:

- qual área será alterada;
- qual comportamento deve permanecer idêntico;
- quais contratos de API serão preservados;
- quais props/exports públicos não podem mudar;
- se haverá impacto visual/UX;
- quais evidências serão necessárias.

8. Classificar risco inicial.

Use:

- baixo: extração pura, shell de compatibilidade, função utilitária sem mudança visual;
- médio: formulário, estado local, props, i18n, hooks ou renderização relevante;
- alto: checkout, escrow, chat, upload, autenticação, rotas, permissões, contratos de API.

9. Criar um plano curto de execução.

O plano deve ter 2 a 5 marcos, por exemplo:

- mapear arquivos e contratos;
- extrair hook/componente/util;
- validar build e diff;
- atualizar documentação/evidências.

10. Se houver impacto frontend/UX, capturar ou planejar evidências antes de editar.

Usar o workflow:

```text
frontend-visual-evidence-pack
```

11. Só iniciar edição depois de declarar:

- escopo;
- arquivos-alvo;
- arquivos evitados;
- validação planejada;
- necessidade ou dispensa de evidência visual.

## Critérios de pronto para começar

- `git status --short` verificado no repo relevante.
- Regras ativas consultadas.
- Escopo pequeno definido.
- Arquivos locais de terceiros protegidos.
- Contratos públicos identificados.
- Evidência visual planejada quando aplicável.
- Próximo passo de implementação claro.
