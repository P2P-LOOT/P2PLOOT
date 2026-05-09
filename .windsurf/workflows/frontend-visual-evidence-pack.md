---
description: Capturar e documentar evidências visuais desktop/mobile para incrementos frontend e UX
---
# Frontend Visual Evidence Pack

Use este workflow para qualquer alteração que toque frontend, visualização, layout, modal, formulário, interação, responsividade ou UX.

## Objetivo

Criar um pacote auditável de evidências visuais antes/depois para acelerar revisão humana, evitar regressões visuais e documentar equivalência ou mudança intencional de UX.

## Quando usar

Use quando o incremento afetar:

- componentes React visíveis;
- telas, modais, cards, tabs, formulários ou menus;
- classes CSS/Tailwind;
- estados de loading, erro, sucesso ou vazio;
- responsividade desktop/mobile;
- fluxo de interação do usuário.

Se for uma refatoração sem mudança visual esperada, ainda assim capture evidências para demonstrar equivalência visual sempre que possível.

## Pasta padrão

Criar ou usar uma pasta no padrão:

```text
/home/ci/Desktop/Projetos/P2PLOOT/docs/evidencias/frontend/incremento-N-nome-curto/
```

Exemplo:

```text
/home/ci/Desktop/Projetos/P2PLOOT/docs/evidencias/frontend/incremento-4-game-filters/
```

## Nomenclatura recomendada

Usar nomes explícitos:

```text
antes-desktop.png
depois-desktop.png
antes-mobile.png
depois-mobile.png
antes-desktop-modal-aberto.png
depois-desktop-modal-aberto.png
antes-mobile-erro.png
depois-mobile-erro.png
```

Quando houver scroll interno, indicar a posição:

```text
antes-add-game-modal-desktop-topo.png
antes-add-game-modal-desktop-final.png
depois-add-game-modal-mobile-topo.png
depois-add-game-modal-mobile-final.png
```

## Passos antes da alteração

1. Rodar o workflow:

```text
frontend-increment-preflight
```

2. Definir o cenário visual.

Registrar:

- rota ou harness usado;
- usuário/perfil;
- dados usados;
- viewport desktop;
- viewport mobile;
- estado da tela;
- interações necessárias para chegar ao estado.

3. Preferir rota real da aplicação.

Se rota real depender de autenticação, dados instáveis ou fluxo muito longo, é permitido usar harness isolado desde que documentado.

Exemplo de harness:

```text
src/__evidence__/nome-do-harness.jsx
```

Importante: se o harness for temporário e não fizer parte do PR, documentar que foi usado apenas para captura. Se ele permanecer no repositório, justificar sua permanência.

4. Capturar estado antes.

Obrigatório quando possível:

- antes desktop;
- antes mobile.

Capturar também estados relevantes:

- modal aberto;
- loading;
- erro;
- sucesso;
- empty state;
- scroll topo/final;
- fluxo intermediário relevante.

5. Se não for possível capturar algum antes, documentar o motivo.

Exemplos de motivo aceitável:

- não existe histórico disponível;
- branch base não executa localmente;
- estado depende de serviço externo indisponível;
- dado antigo não é recuperável.

## Passos depois da alteração

1. Usar o mesmo cenário do antes.

Manter, sempre que possível:

- mesma rota;
- mesmo usuário/perfil;
- mesmos dados;
- mesma viewport;
- mesmo estado da tela;
- mesma posição de scroll.

2. Capturar estado depois.

Obrigatório quando possível:

- depois desktop;
- depois mobile.

3. Comparar visualmente antes/depois.

Classificar o resultado como:

- preservado sem mudança visual esperada;
- mudança intencional de UX.

4. Se houver mudança intencional, documentar:

- o que mudou;
- por que mudou;
- impacto esperado na revisão/auditoria;
- se a mudança foi solicitada ou surgiu da refatoração.

## Atualização documental

Adicionar seção de evidência no incremento correspondente em:

```text
/home/ci/Desktop/Projetos/P2PLOOT/MODULARIZACAO_INCREMENTAL_FRONTEND.md
```

Template:

```md
### Evidências visuais

Status: documentado com comparação antes/depois em desktop e mobile.

Contexto validado:

- Área:
- Rota/harness:
- Perfil/usuário:
- Dados usados:
- Viewports:
- Estados capturados:

Prints:

- Antes desktop: `docs/evidencias/frontend/incremento-N-nome-curto/antes-desktop.png`
- Depois desktop: `docs/evidencias/frontend/incremento-N-nome-curto/depois-desktop.png`
- Antes mobile: `docs/evidencias/frontend/incremento-N-nome-curto/antes-mobile.png`
- Depois mobile: `docs/evidencias/frontend/incremento-N-nome-curto/depois-mobile.png`

Resultado visual:

- Preservado sem mudança visual esperada; ou
- Mudança intencional de UX:
  - o que mudou;
  - por que mudou;
  - impacto esperado.
```

## Validação relacionada

Depois de capturar evidências, confirmar:

```bash
git diff --check
```

E validar build:

```bash
VITE_API_URL=http://localhost:6110 npm run build
```

Diretório do build:

```text
/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-frontend
```

## Critérios de sucesso

- Há evidência antes/depois em desktop e mobile ou motivo documentado para ausência.
- Prints usam cenário equivalente.
- A pasta segue o padrão `docs/evidencias/frontend/incremento-N-nome-curto/`.
- A documentação do incremento referencia os arquivos.
- O resultado visual foi classificado como preservado ou mudança intencional.
