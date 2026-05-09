# Modularização Incremental do Frontend

## Objetivo

Este documento registra a estratégia incremental de modularização do frontend do P2PLOOT, as mudanças já realizadas e os próximos blocos recomendados.

A motivação principal é reduzir arquivos monolíticos, separar responsabilidades e facilitar manutenção com agentes de IA, evitando que mudanças pequenas dependam da leitura de blocos grandes e pouco relacionados.

## Escopo

- **Incluído:** frontend em `p2ploot-frontend`.
- **Excluído:** backend e contratos de API.
- **Premissa:** preservar comportamento existente e APIs públicas do frontend sempre que possível.
- **Cuidados:** evitar mexer em arquivos com alterações locais pré-existentes quando não forem necessários para o incremento atual.

## Princípios de modularização

1. **Incrementos pequenos e validáveis**
   - Modularizar uma área por vez.
   - Rodar build após cada bloco relevante.
   - Evitar refatorações amplas sem validação intermediária.

2. **Preservar contratos públicos**
   - Manter exports usados por rotas e outros componentes.
   - Quando possível, usar arquivos de compatibilidade temporários.

3. **Separar responsabilidades**
   - Componentes para UI.
   - Hooks para estado, efeitos e operações assíncronas.
   - Utils para mapeamento, permissões e transformações puras.
   - Páginas/containers para orquestração.

4. **Não alterar backend**
   - Nenhuma mudança deve exigir ajuste de API.
   - Normalizações de payload devem ficar no frontend quando necessário.

5. **Facilitar iteração com IA**
   - Preferir arquivos menores e com responsabilidade clara.
   - Documentar a intenção de cada extração.
   - Evitar misturar UI, regra de negócio, chamada de API e transformação de dados no mesmo arquivo.

## Estado inicial relevante

O arquivo `src/components/Web3Games/Web3GamesPage.jsx` concentrava múltiplas responsabilidades em um único bloco grande, incluindo:

- página principal do catálogo de jogos;
- cards de jogos;
- página de detalhes de jogo;
- página de detalhes de blockchain;
- modal de edição;
- comentários e respostas;
- filtros;
- favoritos;
- votação;
- mapeamento de dados da API;
- regras de permissão.

Esse arquivo foi escolhido como primeiro bloco P0 por ter alto impacto na manutenibilidade e boas fronteiras internas para extração.

## Incremento 1 concluído: Web3Games

### Resumo

O monólito `Web3GamesPage.jsx` foi dividido em componentes, hooks e utilitários dentro da própria pasta da feature `Web3Games`.

O arquivo original foi preservado como camada de compatibilidade, mantendo exports esperados pelas rotas existentes.

### Arquivo de compatibilidade

- `src/components/Web3Games/Web3GamesPage.jsx`

Agora reexporta:

- `default` de `Web3GamesHome.jsx`;
- `GameDetailsPage` de `GameDetailsPage.jsx`;
- `BlockchainDetailsPage` de `BlockchainDetailsPage.jsx`.

### Componentes criados

- `src/components/Web3Games/Web3GamesHome.jsx`
  - Orquestra a página principal do catálogo.
  - Usa hooks de catálogo, favoritos e votação.
  - Renderiza filtros, busca, seções de jogos e modais.

- `src/components/Web3Games/GameCard.jsx`
  - Renderiza o card individual de jogo.
  - Mantém ações de navegação, favorito, voto e edição.
  - Passou a usar `canManageGame` para checagem de permissão.

- `src/components/Web3Games/FilterAccordion.jsx`
  - Renderiza filtros expansíveis por gênero e blockchain.
  - Usa utilitário de slug para chaves de tradução.

- `src/components/Web3Games/EditGameModal.jsx`
  - Modal de edição de jogos extraído do monólito.
  - Mantém upload de imagem, edição de campos, regiões/servidores, reset de estatísticas e exclusão.

- `src/components/Web3Games/GameDetailsPage.jsx`
  - Página de detalhes de jogo.
  - Mantém busca de dados, renderização técnica, comentários e links externos.

- `src/components/Web3Games/BlockchainDetailsPage.jsx`
  - Página de detalhes de blockchain.
  - Mantém listagem de jogos relacionados e navegação para detalhes.

- `src/components/Web3Games/CommentItem.jsx`
  - Renderiza comentários e respostas aninhadas.
  - Isola UI de voto, resposta, expandir/colapsar e exclusão.

### Hooks criados

- `src/components/Web3Games/hooks/useGamesCatalog.js`
  - Busca jogos via `api.getGames()`.
  - Normaliza dados com `mapApiGameToCatalogGame`.
  - Expõe `games`, `setGames`, `loading`, `error` e `fetchGames`.

- `src/components/Web3Games/hooks/useGameVoting.js`
  - Gerencia votos em jogos.
  - Mantém estado local em `localStorage`.
  - Sincroniza votos de jogos persistidos com `api.voteGame` quando há `userId`.

- `src/components/Web3Games/hooks/useGameFavorites.js`
  - Gerencia favoritos em `localStorage`.
  - Evita toggles duplicados em curto intervalo.

- `src/components/Web3Games/hooks/useGameComments.js`
  - Centraliza carregamento, criação, votação e exclusão de comentários.
  - Mantém estados de resposta e postagem.

### Utilitários criados

- `src/components/Web3Games/utils/gameMappers.js`
  - Centraliza normalização de dados da API para formatos usados no catálogo, detalhes e jogos relacionados.
  - Define fallback de imagem.

- `src/components/Web3Games/utils/gamePermissions.js`
  - Centraliza regra de permissão para gerenciamento de jogo.
  - Trata permissões em array ou JSON serializado.
  - Evita quebra caso permissões venham em formato inválido.

- `src/components/Web3Games/utils/translationSlug.js`
  - Normaliza textos para chaves de tradução.

### Rotas atualizadas

- `src/App.jsx`
  - `GameDetailsPage` passou a ser lazy-loaded diretamente de `./components/Web3Games/GameDetailsPage`.
  - `BlockchainDetailsPage` passou a ser lazy-loaded diretamente de `./components/Web3Games/BlockchainDetailsPage`.
  - `Web3GamesPage` continua apontando para `./components/Web3Games/Web3GamesPage` por compatibilidade.

### Resultado em tamanho de arquivos

Após a extração, os principais arquivos da feature ficaram com responsabilidades menores:

| Arquivo | Linhas aproximadas |
| --- | ---: |
| `Web3GamesPage.jsx` | 3 |
| `Web3GamesHome.jsx` | 219 |
| `GameDetailsPage.jsx` | 295 |
| `BlockchainDetailsPage.jsx` | 136 |
| `EditGameModal.jsx` | 390 |
| `GameCard.jsx` | 112 |
| `CommentItem.jsx` | 132 |
| `FilterAccordion.jsx` | 52 |
| `hooks/useGamesCatalog.js` | 31 |
| `hooks/useGameVoting.js` | 66 |
| `hooks/useGameFavorites.js` | 42 |
| `hooks/useGameComments.js` | 141 |
| `utils/gameMappers.js` | 46 |
| `utils/gamePermissions.js` | 23 |
| `utils/translationSlug.js` | 7 |

## Validação realizada

### Build

O build de produção foi validado com variável local de API:

```bash
VITE_API_URL=http://localhost:6110 npm run build
```

Resultado: build concluído com sucesso.

Observação: `npm run build` sem `VITE_API_URL` falha por regra já existente em `vite.config.js`, que exige essa variável em modo production.

### Lint

`npm run lint` não pôde ser usado como validação efetiva porque o projeto não possui configuração ESLint detectável no estado atual.

A falha ocorre antes da análise dos arquivos modificados.

## Arquivos com alterações locais pré-existentes evitados

Durante este incremento, os seguintes arquivos já apareciam modificados no status do Git e não foram usados como alvo da modularização:

- `src/components/Guild/GuildAuctionSection.jsx`
- `src/components/PlayerMarket/AdDetailsModal.jsx`
- `src/lib/api.js`

## Incremento 2 concluído: redução de `EditGameModal.jsx`

### Resumo

O `EditGameModal.jsx` foi reduzido para atuar como shell/orquestrador visual do modal de edição.

As responsabilidades de formulário, upload de imagem, regiões/servidores e ações assíncronas foram separadas em componentes e hook dedicados dentro da feature `Web3Games`.

### Componentes criados

- `src/components/Web3Games/components/form/EditGameFormFields.jsx`
  - campos básicos do jogo;
  - gênero, modo, blockchain, status, token e NFT info;
  - descrições, detalhes, requisitos e site oficial.

- `src/components/Web3Games/components/form/GameImageUploadField.jsx`
  - preview de imagem;
  - upload;
  - input manual de URL;
  - estado visual de upload.

- `src/components/Web3Games/components/form/ServerRegionsEditor.jsx`
  - adicionar/remover região;
  - adicionar/remover servidores;
  - renderização de chips por servidor.

### Hook criado

- `src/components/Web3Games/hooks/useEditGameForm.js`
  - estado do formulário;
  - hidratação a partir do jogo selecionado;
  - handlers de mudança;
  - upload de imagem;
  - submit/update/delete/reset;
  - manipulação de regiões e servidores.

### Commits do incremento

- `refactor(web3games): extract edit game form components`
- `refactor(web3games): extract edit game form hook`

### Validação

- `VITE_API_URL=http://localhost:6110 npm run build` executado com sucesso após a extração de componentes.
- `VITE_API_URL=http://localhost:6110 npm run build` executado com sucesso após a extração do hook.

### Contratos preservados

- Nenhuma mudança no contrato de `api.updateGame`.
- Nenhuma mudança no contrato de `api.deleteGame`.
- Nenhuma mudança no contrato de `api.uploadImage`.
- Nenhuma mudança esperada nas props públicas de `EditGameModal`.

## Próximos incrementos recomendados

### Incremento 3: aproximar `AddGameModal.jsx` e `EditGameModal.jsx`

Motivo: os dois modais tendem a ter campos, upload e regiões/servidores semelhantes.

Ações recomendadas:

- Extrair componentes compartilhados para `Web3Games/components/form/`.
- Reutilizar `GameImageUploadField`.
- Reutilizar `ServerRegionsEditor`.
- Avaliar um hook base `useGameForm` com modos `create` e `edit`.

Critério de sucesso:

- Redução de duplicidade visual e lógica.
- Fluxos de criação e edição preservados.

### Incremento 4: extrair lógica de filtros do catálogo

Motivo: `Web3GamesHome.jsx` ainda concentra cálculo de filtros e seções.

Extrações recomendadas:

- `hooks/useGameFilters.js`
  - gênero selecionado;
  - blockchain selecionada;
  - busca;
  - abertura dos accordions;
  - listas derivadas de gêneros/blockchains;
  - função `clearFilters`.

- `utils/gameFilters.js`
  - filtro puro por status, gênero, blockchain e busca.

Critério de sucesso:

- `Web3GamesHome.jsx` ficar focado em layout e composição.
- Filtros testáveis como função pura.

### Incremento 5: separar seções visuais do catálogo

Motivo: a página principal pode ficar ainda mais fácil de alterar por agentes de IA.

Extrações recomendadas:

- `components/GamesSection.jsx`
  - título;
  - ícone;
  - lista de jogos;
  - empty state;
  - props para voto, favorito e edição.

- `components/GamesSidebarFilters.jsx`
  - sidebar completa de filtros;
  - uso de `FilterAccordion`.

- `components/GamesSearchBar.jsx`
  - input de busca.

Critério de sucesso:

- `Web3GamesHome.jsx` ficar como composição de alto nível.

### Incremento 6: comentários de jogos

Motivo: `GameDetailsPage.jsx` ainda contém layout da seção de comentários e função de formatação de datas.

Extrações recomendadas:

- `components/GameCommentsSection.jsx`
  - formulário de novo comentário;
  - empty/loading states;
  - lista de `CommentItem`.

- `utils/dateFormatters.js`
  - `formatRelativeDate` com suporte a i18n.

Critério de sucesso:

- `GameDetailsPage.jsx` ficar focado em hero, dados técnicos e composição.

### Incremento 7: dados técnicos e hero de detalhes

Motivo: a página de detalhes ainda possui blocos visuais grandes.

Extrações recomendadas:

- `components/GameDetailsHero.jsx`
- `components/GameTechnicalDataCard.jsx`
- `components/GameAboutSection.jsx`

Critério de sucesso:

- Cada componente representar uma região clara da tela.

### Incremento 8: página de blockchain

Motivo: `BlockchainDetailsPage.jsx` já está menor, mas pode ser mais modular.

Extrações recomendadas:

- `components/BlockchainHero.jsx`
- `components/BlockchainInfoCard.jsx`
- `components/RelatedGamesGrid.jsx`
- `hooks/useBlockchainRelatedGames.js`

Critério de sucesso:

- Página com menos lógica inline.
- Reuso de card/lista para jogos relacionados, se fizer sentido.

## Ordem sugerida de execução

1. Modularizar `EditGameModal.jsx`.
2. Reutilizar partes entre `AddGameModal.jsx` e `EditGameModal.jsx`.
3. Extrair filtros e seções de `Web3GamesHome.jsx`.
4. Modularizar seção de comentários em `GameDetailsPage.jsx`.
5. Modularizar hero/dados técnicos da página de detalhes.
6. Modularizar blocos menores da página de blockchain.
7. Reavaliar outros P0 do relatório original após estabilizar `Web3Games`.

## Checklist por incremento

Antes de iniciar:

- Verificar `git status --short`.
- Confirmar se há arquivos com alterações locais de outro desenvolvedor.
- Escolher um bloco com fronteira clara.

Durante a alteração:

- Evitar mudanças de comportamento não solicitadas.
- Não alterar backend.
- Preservar imports e exports usados por rotas.
- Preferir funções puras em `utils` quando possível.
- Preferir hooks para estado e efeitos.

Após a alteração:

- Rodar build com `VITE_API_URL` local.
- Conferir se `dist` não entrou no Git status, caso não seja esperado.
- Registrar arquivos criados/modificados.
- Atualizar este documento com o incremento concluído.

## Comando de validação recomendado

```bash
VITE_API_URL=http://localhost:6110 npm run build
```

Caso exista uma URL real de API para ambiente de desenvolvimento, ela pode substituir `http://localhost:6110` apenas para validação do build.
