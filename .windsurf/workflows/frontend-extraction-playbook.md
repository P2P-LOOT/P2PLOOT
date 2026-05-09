---
description: Modularizar frontend com extrações pequenas, contratos preservados e arquivos revisáveis
---
# Frontend Extraction Playbook

Use este workflow quando a tarefa for extrair componentes, hooks, utils, seções, modais, tabs ou lógica de arquivos grandes do frontend.

## Objetivo

Padronizar a modularização para melhorar manutenção, revisão humana e iteração agentica sem alterar backend ou contratos públicos sem pedido explícito.

## Princípios

1. Manter incrementos pequenos e validáveis.
2. Preservar comportamento existente por padrão.
3. Separar responsabilidade por tipo de arquivo:
   - componentes para UI;
   - hooks para estado, efeitos e operações assíncronas;
   - utils para transformações puras;
   - shells/containers para orquestração.
4. Preservar props, exports, rotas e chamadas de API quando possível.
5. Usar arquivos de compatibilidade temporários quando uma importação pública existente não puder ser removida com segurança.
6. Evitar tocar backend durante modularizações frontend.

## Passos

1. Rodar primeiro o workflow:

```text
frontend-increment-preflight
```

2. Mapear responsabilidades do arquivo alvo.

Classificar blocos em:

- renderização visual;
- estado local;
- efeitos/fetch;
- submit/actions;
- permissões;
- mapeamento de payload;
- filtros/derivações;
- formatação;
- modais/tabs/seções.

3. Escolher apenas uma fronteira de extração por incremento.

Bons exemplos:

- extrair filtros de catálogo para `useGameFilters` e `gameFilters`;
- extrair seção visual para `GamesSection`;
- extrair formulário de modal para componentes dedicados;
- extrair lógica comum de criação/edição para hook compartilhado.

Evitar no mesmo incremento:

- alterar UI e contrato de API juntos;
- refatorar várias features ao mesmo tempo;
- misturar bugfix não relacionado;
- reorganizar pastas amplamente sem validação intermediária.

4. Definir destino da extração.

Padrões recomendados:

```text
src/components/<Feature>/components/<NomeComponente>.jsx
src/components/<Feature>/hooks/useNomeDoHook.js
src/components/<Feature>/utils/nomeDaUtil.js
```

Para `Web3Games`, manter dentro de:

```text
src/components/Web3Games/
```

5. Extrair UI para componente quando o bloco:

- recebe dados por props;
- não precisa conhecer API diretamente;
- não precisa controlar fluxo global;
- representa uma seção clara da tela.

Checklist do componente:

- props explícitas;
- sem fetch interno, salvo se a responsabilidade for claramente isolada;
- sem alterar texto/layout além do necessário;
- export default ou named export consistente com a pasta.

6. Extrair lógica para hook quando o bloco contém:

- `useState` relacionado a um fluxo;
- `useEffect`;
- loading/error;
- submit/action async;
- manipulação de formulário;
- upload;
- sincronização com `localStorage`.

Checklist do hook:

- nome iniciado com `use`;
- recebe dependências por parâmetro;
- expõe estado e handlers mínimos;
- não acopla UI desnecessariamente;
- preserva mensagens/side effects existentes, exceto quando a tarefa pedir melhoria.

7. Extrair util quando a lógica for pura.

Bons candidatos:

- filtros;
- mapeadores de API;
- normalização de strings;
- permissões;
- formatação de datas/valores;
- cálculo derivado.

Checklist da util:

- sem React;
- sem `window`, `localStorage` ou API, salvo util explicitamente destinada a isso;
- entrada e saída fáceis de testar manualmente;
- sem efeito colateral.

8. Manter o arquivo original como shell quando ele for rota, modal público ou import existente.

O shell deve:

- preservar export default/named exports;
- preservar props públicas;
- compor os módulos extraídos;
- concentrar apenas orquestração de alto nível.

9. Validar imports após cada bloco de extração.

Checar especialmente:

- caminhos relativos;
- imports não usados;
- exports usados por rotas;
- props obrigatórias;
- nomes de campos enviados para API.

10. Preservar contratos de API.

Durante modularização:

- não alterar payload de `api.*` sem motivo explícito;
- não renomear campos enviados ao backend;
- não mudar endpoint;
- não mudar fluxo de autenticação;
- documentar qualquer normalização frontend.

11. Rodar validação mínima ao concluir o bloco:

```bash
git diff --check
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-frontend
```

12. Rodar build de frontend:

```bash
VITE_API_URL=http://localhost:6110 npm run build
```

Diretório:

```text
/home/ci/Desktop/Projetos/P2PLOOT/p2ploot-frontend
```

13. Se a extração afetar visualização, layout, interação ou UX, usar:

```text
frontend-visual-evidence-pack
```

14. Preparar revisão final com:

```text
frontend-review-bundle
```

## Critérios de sucesso

- O arquivo original ficou menor e mais focado.
- Cada novo arquivo tem responsabilidade clara.
- Contratos de API, props e exports públicos foram preservados.
- Build passou com `VITE_API_URL=http://localhost:6110`.
- Diferença visual foi documentada ou explicitamente classificada como preservada.
- O PR pode ser revisado por blocos pequenos.
