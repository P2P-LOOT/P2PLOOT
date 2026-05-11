# Plano de Desenvolvimento P0 - Modularizacao Frontend

Data: 10/05/2026  
Escopo: `p2ploot-frontend`  
Tipo: plano incremental de desenvolvimento, validacao e tratamento de erros

## Objetivo

Executar a sequencia P0 de modularizacao do frontend com baixo risco operacional, preservando comportamento, rotas, props publicas e contratos de API.

A sequencia prioriza:

1. estabilizar a fonte de jogos;
2. fatiar `src/lib/api.js` por dominio;
3. quebrar `AdDetailsModal.jsx`;
4. quebrar `GuildAuctionSection.jsx`.

## Premissas

- Nenhuma etapa deve exigir mudanca de backend.
- Imports publicos existentes devem continuar funcionando durante a transicao.
- Cada PR deve ser pequeno, revisavel e validavel isoladamente.
- Refatoracoes devem ser por extracao, nao por reescrita.
- Mudancas visuais esperadas devem ter evidencia antes/depois.
- Arquivos com alteracoes locais fora do escopo nao devem ser revertidos.

## Checklist Global Antes de Cada PR

- Rodar `git status --short` na raiz do repo.
- Rodar `git status --short && git branch --show-current` em `p2ploot-frontend`.
- Identificar arquivos ja modificados e separar o que pertence ao incremento.
- Confirmar que o PR toca apenas uma fronteira de responsabilidade.
- Confirmar contratos preservados: rotas, props, exports e chamadas `api.*`.
- Confirmar se ha impacto visual/UX e quais evidencias serao capturadas.
- Rodar build ou registrar explicitamente por que nao foi possivel.

Comando base de build:

```bash
cd p2ploot-frontend
VITE_API_URL=http://localhost:6110 npm run build
```

## Tratamento Global de Erros

### Erro de build

1. Ler a primeira falha real do build, nao apenas o ultimo stack trace.
2. Verificar se a falha esta em arquivo tocado no PR.
3. Se a falha vier de import/export quebrado, restaurar a compatibilidade antes de continuar.
4. Se a falha vier de lint/config preexistente, documentar e validar por teste manual focado.

Checklist:

- Erro reproduzido localmente.
- Arquivo responsavel identificado.
- Import/export revisado.
- Build reexecutado apos correcao.
- Se nao corrigido, risco documentado no PR.

### Erro de runtime

1. Reproduzir no fluxo manual correspondente.
2. Verificar console do navegador.
3. Conferir se estado/props migrados para hook mantiveram valores iniciais.
4. Conferir se chamadas `api.*` continuam com mesma assinatura.
5. Corrigir primeiro a compatibilidade, depois limpar codigo.

Checklist:

- Rota afetada identificada.
- Console revisado.
- Payload de API comparado antes/depois quando aplicavel.
- Estado inicial e loading state preservados.
- Empty/error state preservado.

### Erro de comportamento visual

1. Comparar print antes/depois usando mesma viewport e mesmos dados.
2. Verificar se a extracao mudou classes, ordem de renderizacao ou condicional.
3. Se o PR era refatoracao pura, priorizar equivalencia visual.

Checklist:

- Desktop comparado.
- Mobile comparado quando fluxo for responsivo.
- Modal/dropdown/loading relevante comparado.
- Diferencas intencionais documentadas.

### Estrategia de rollback

- Preferir rollback do ultimo commit/PR, nao reversao manual dispersa.
- Se a falha estiver em uma extracao, manter o novo arquivo e restaurar temporariamente o container antigo somente se isso reduzir risco.
- Nao remover arquivos de compatibilidade no mesmo PR em que eles forem criados.

Checklist:

- O PR tem fronteira clara para revert.
- Arquivos de compatibilidade foram mantidos.
- Remocoes definitivas foram adiadas para PR posterior.

## PR 0 - Baseline de Seguranca

### Objetivo

Criar uma referencia de comportamento antes da sequencia P0.

### Entregaveis

- Checklist manual dos fluxos criticos.
- Resultado de build registrado.
- Lista de arquivos alterados/preexistentes registrada.
- Evidencias visuais quando houver tela afetada.

### Fluxos manuais minimos

- Abrir catalogo de jogos.
- Abrir detalhe de jogo API (`db-*`).
- Abrir detalhe de jogo legado, se ainda aplicavel.
- Abrir pagina de blockchain.
- Criar/editar anuncio no market.
- Abrir `AdDetailsModal`.
- Iniciar ou visualizar conversa.
- Verificar bloco de escrow quando disponivel.
- Abrir gestao de guilda.
- Abrir leiloes ativos.
- Abrir warehouse.
- Abrir historico de leiloes.

### Tratamento de erros

- Se a API local nao estiver disponivel, registrar que o build foi feito com `VITE_API_URL`, mas validacao funcional ficou pendente.
- Se dados locais forem insuficientes, validar pelo menos loading, empty state e navegacao.
- Se alguma rota ja estiver quebrada antes do PR, registrar como baseline quebrada e nao tentar corrigir junto.

### Criterio de aceite

- Baseline documentada.
- Build executado ou impedimento documentado.
- Fluxos criticos listados com status.
- Nenhuma mudanca funcional feita neste PR alem de documentacao/checklist.

## PR 1 - Estabilizar Fonte de Jogos

### Objetivo

Ter uma fonte canoncica para dados de jogos no frontend e reduzir duplicacao entre `useGames`, `useGamesCatalog`, `gamesData` e mappers locais.

### Arquivos alvo

- `p2ploot-frontend/src/hooks/useGames.js`
- `p2ploot-frontend/src/components/Web3Games/hooks/useGamesCatalog.js`
- `p2ploot-frontend/src/components/Web3Games/utils/gameMappers.js`
- `p2ploot-frontend/src/components/Web3Games/GameDetailsPage.jsx`
- `p2ploot-frontend/src/components/Web3Games/BlockchainDetailsPage.jsx`
- `p2ploot-frontend/src/data/gamedata.js`
- consumidores de `api.getGames()` em market/guild/profile quando aplicavel

### Sequencia de desenvolvimento

1. Mapear todos os consumidores de jogos.
2. Definir o shape canonico de jogo para UI.
3. Consolidar mappers em um unico modulo.
4. Escolher um hook publico recomendado.
5. Manter fallback legado documentado.
6. Remover duplicacao somente quando houver consumidor migrado.

### Tratamento de erros especifico

#### API retorna campos em camelCase/snake_case

- Manter normalizacao em mapper unico.
- Evitar normalizacao solta dentro de componentes.
- Cobrir `serverRegions/server_regions`, `imageUrl/image_url`, `tokenId/token_id`, `nftInfo/nft_info`.

Checklist:

- Mapper cobre campos camelCase.
- Mapper cobre campos snake_case.
- Fallback de imagem preservado.
- Regioes/servidores preservados para criacao/edicao de anuncios.

#### Id legado vs `db-*`

- Documentar comportamento esperado.
- Nao remover suporte a id numerico enquanto houver rota ou dado estatico consumindo esse formato.
- Tratar parse de id em funcao utilitaria, nao inline em varios componentes.

Checklist:

- `db-*` abre detalhe via API.
- Id legado abre detalhe local quando ainda suportado.
- Comentarios usam `dbId` correto.
- Links de cards continuam usando formato esperado.

#### Blockchain sem jogos relacionados

- Diferenciar erro de API de lista vazia.
- Normalizar nome/slug de blockchain antes de comparar.
- Documentar fragilidade enquanto nao houver endpoint filtrado.

Checklist:

- Blockchain existente encontra dados estaticos.
- Jogos relacionados carregam ou exibem empty state.
- Erro de API nao quebra a pagina inteira.

### Verificacao

- Catalogo `/pt/games` carrega.
- Filtros de genero e blockchain funcionam.
- Busca continua funcionando.
- Detalhe de jogo API carrega.
- Detalhe legado continua funcionando ou remocao foi explicitamente aprovada.
- Pagina de blockchain carrega.
- Jogos relacionados aparecem quando existem.
- Fluxos de anuncio que dependem de lista de jogos continuam funcionando.
- Build passa.

### Criterio de aceite

- Ha um hook recomendado para catalogo/lista de jogos.
- Ha um mapper canonico.
- Duplicacao de normalizacao foi reduzida.
- Transicao `db-*` vs legado esta documentada.
- Nenhum fluxo consumidor perdeu dados obrigatorios.

## PR 2 - Fatiar `api.js` Por Dominio

### Objetivo

Separar o cliente de API por dominio mantendo compatibilidade com `import { api } from '../../lib/api'`.

### Estrutura sugerida

```text
src/lib/api/
  request.js
  auth.js
  profile.js
  games.js
  guild.js
  market.js
  support.js
  admin.js
  events.js
  web3.js
  index.js
src/lib/api.js
```

### Sequencia de desenvolvimento

1. Criar `src/lib/api/request.js` com `request()` e `getApiUrl()`.
2. Mover um dominio por vez para arquivos separados.
3. Exportar objetos por dominio.
4. Compor `api` em `src/lib/api/index.js`.
5. Manter `src/lib/api.js` como barrel de compatibilidade.
6. So remover duplicacoes depois que o build passar.

### Tratamento de erros especifico

#### Import quebrado

- Confirmar se consumidor importa `../../lib/api` ou `../../lib/api.js`.
- Manter ambos funcionando se ja existirem no repo.
- Evitar exigir alias novo neste PR.

Checklist:

- `import { api } from '../../lib/api'` ainda funciona.
- `import { api } from './lib/api'` ainda funciona.
- Nao ha import direto para arquivo de dominio antes da compatibilidade estar estavel.

#### Assinatura de endpoint alterada por engano

- Mover funcoes sem mudar parametros.
- Nao renomear metodos neste PR.
- Nao alterar paths de endpoint.

Checklist:

- `api.getGames()` preservado.
- `api.getGame(id)` preservado.
- `api.getGuild(id)` preservado.
- `api.getAds(filters)` preservado.
- `api.sendMessage(conversationId, content)` preservado.
- `api.createEscrow(...)` e web3 relacionados preservados.

#### Dependencia circular

- `request.js` nao deve importar dominios.
- Arquivos de dominio importam apenas `request`.
- `index.js` compoe dominios.

Checklist:

- `request.js` sem import de `api`.
- Dominios sem import entre si, salvo necessidade clara.
- `index.js` e o unico agregador.

### Verificacao

- Build passa.
- Login/profile ainda carrega.
- Catalogo de jogos ainda chama `api.getGames`.
- Guild management ainda chama endpoints de guild.
- Market ainda chama endpoints de ads/conversations.
- Support/admin/events ainda compilam.
- Nao ha mudanca funcional intencional.

### Criterio de aceite

- `api.js` ficou como camada de compatibilidade.
- Dominios principais foram separados.
- Nenhum consumidor precisou mudar obrigatoriamente.
- Duplicacao de `getGames` foi resolvida ou registrada para PR seguinte.

## PR 3 - Quebrar `AdDetailsModal`

### Objetivo

Transformar `AdDetailsModal.jsx` em container de orquestracao, separando chat, escrow, edicao, denuncia e galeria.

### Arquivos alvo

- `p2ploot-frontend/src/components/PlayerMarket/AdDetailsModal.jsx`
- novos arquivos em `p2ploot-frontend/src/components/PlayerMarket/components/`
- novos hooks em `p2ploot-frontend/src/components/PlayerMarket/hooks/`
- utils locais em `p2ploot-frontend/src/components/PlayerMarket/utils/`

### Sequencia de desenvolvimento

1. Extrair helpers puros para `utils/adDetails.js`.
2. Extrair componentes visuais sem mover estado.
3. Extrair `useAdConversation`.
4. Extrair `useAdEditForm`.
5. Extrair `useAdReportFlow`.
6. Extrair `useEscrowFlow` por ultimo.
7. Reduzir o container principal somente depois de cada hook estar validado.

### Tratamento de erros especifico

#### Chat nao carrega ou nao envia

- Verificar `conversationId`.
- Verificar se `messages` manteve ordem e shape.
- Verificar se envio ainda usa retorno esperado da API.
- Se otimizacao de refetch for aplicada, manter fallback para refetch em caso de resposta incompleta.

Checklist:

- Abrir anuncio sem conversa existente.
- Abrir anuncio com conversa existente.
- Enviar mensagem como comprador.
- Enviar/visualizar mensagem como vendedor, se aplicavel.
- Loading e erro de mensagens preservados.

#### Escrow quebra estado ou acao

- Migrar por ultimo.
- Manter nomes de estados expostos pelo hook iguais aos usados no JSX ate estabilizar.
- Nao alterar calculo de valores, taxas ou status labels junto da extracao.

Checklist:

- Status de escrow renderiza.
- Acoes disponiveis continuam condicionadas ao perfil correto.
- Loading de acao nao trava.
- Erro de API exibe feedback.
- Nenhuma assinatura web3/api foi alterada.

#### Edicao perde campos

- Conferir inicializacao de `editFormData`.
- Conferir upload e imagens.
- Conferir lista de jogos/regioes/servidores vinda da fonte canonica.

Checklist:

- Entrar em modo edicao.
- Alterar texto/preco/campos basicos.
- Alterar imagem quando possivel.
- Selecionar jogo/regiao/servidor.
- Salvar e fechar modal.

#### Denuncia/disputa perde evidencia

- Manter estado de arquivos separado.
- Nao misturar fluxo de denuncia com escrow no primeiro corte.
- Conferir FormData/upload quando houver arquivo.

Checklist:

- Abrir painel de denuncia/disputa.
- Preencher motivo.
- Anexar arquivo quando suportado.
- Enviar e receber feedback.

### Verificacao

- Modal abre e fecha sem erro.
- Galeria/lightbox continuam funcionando.
- Chat funciona.
- Edicao funciona.
- Escrow renderiza e acoes principais funcionam.
- Denuncia/disputa funciona ou estado pendente documentado.
- Build passa.
- Arquivo principal reduziu responsabilidade sem alterar UX.

### Criterio de aceite

- `AdDetailsModal.jsx` ficou majoritariamente como container.
- Hooks novos possuem responsabilidade clara.
- Componentes novos recebem props explicitas.
- Nenhuma mudanca de contrato API foi introduzida.
- Fluxos criticos do market foram validados.

## PR 4 - Quebrar `GuildAuctionSection`

### Objetivo

Separar dados, preferencias, filtros, acoes em lote e views de `GuildAuctionSection.jsx`.

### Arquivos alvo

- `p2ploot-frontend/src/components/Guild/GuildAuctionSection.jsx`
- novos arquivos em `p2ploot-frontend/src/components/Guild/auction/components/`
- novos hooks em `p2ploot-frontend/src/components/Guild/auction/hooks/`
- utils locais em `p2ploot-frontend/src/components/Guild/auction/utils/`

### Sequencia de desenvolvimento

1. Extrair constantes e formatadores.
2. Extrair `useAuctionPreferences` para `localStorage`, tab, view mode e filtros persistidos.
3. Extrair `useGuildAuctionsData` para fetch, loading, refresh, paginacao.
4. Extrair `useAuctionFilters` para filtros derivados.
5. Extrair views por tab mantendo estado no container.
6. Extrair `useAuctionBulkActions`.
7. Extrair `useAuctionItemForm` para warehouse/autocomplete/upload.
8. Extrair modais de publish/import por ultimo.

### Tratamento de erros especifico

#### Fetch de leiloes falha

- Separar loading inicial de refresh silencioso.
- Preservar dados antigos quando auto-refresh falhar.
- Exibir feedback sem limpar tela indevidamente.

Checklist:

- Loading inicial aparece.
- Erro inicial nao causa tela quebrada.
- Refresh silencioso preserva dados antigos.
- `onBidPlaced` continua sendo chamado quando necessario.

#### `localStorage` ou preferencias quebram

- Usar fallback seguro quando chave nao existir.
- Resetar pagina/selecoes quando filtros mudarem.
- Preservar chaves antigas durante a transicao.

Checklist:

- Aba ativa persiste.
- View grid/list persiste.
- Filtros persistem.
- Selecoes sao limpas ao trocar tab/filtro.

#### Filtros/paginacao retornam lista errada

- Manter filtro de historico separado do filtro ativo/warehouse.
- Recalcular total de paginas apos cada fetch/filtro.
- Resetar `currentPage` quando busca/filtro muda.

Checklist:

- Busca em leiloes ativos funciona.
- Filtros de raridade/classe/nft funcionam.
- Historico filtra delivered/pending/all.
- Paginacao nao aponta para pagina vazia apos filtro.

#### Bulk actions afetam itens errados

- Guardar selecao por id estavel.
- Limpar selecao apos publish/delete/delivery.
- Bloquear acao enquanto processamento estiver ativo.

Checklist:

- Selecionar item individual.
- Selecionar varios itens.
- Publicar em lote.
- Marcar delivery quando aplicavel.
- Limpar historico quando aplicavel.
- Selecoes antigas nao sobrevivem a filtros/tabs.

#### Upload/importacao falha

- Tratar arquivo invalido.
- Preservar drafts importados ate confirmacao.
- Mostrar erro sem fechar modal.

Checklist:

- Criar item manual.
- Upload de imagem funciona ou erro aparece.
- Importacao aceita formato valido.
- Importacao rejeita formato invalido com feedback.

### Verificacao

- Abrir painel de leiloes pela guilda.
- Voltar para dashboard.
- Alternar tabs.
- Ver leiloes ativos.
- Dar lance.
- Abrir warehouse.
- Criar item.
- Publicar item.
- Usar filtros.
- Usar favoritos.
- Abrir historico.
- Usar paginacao.
- Auto-refresh nao duplica requests visivelmente.
- Build passa.

### Criterio de aceite

- `GuildAuctionSection.jsx` foi reduzido por extracao.
- Dados, preferencias, filtros e bulk actions estao separados.
- Views principais estao em componentes proprios.
- Nenhuma regra de negocio de leilao foi alterada intencionalmente.
- Fluxos de leilao foram validados manualmente.

## Validacao Final da Sequencia P0

- Build passa apos os quatro PRs.
- Catalogo de jogos funcional.
- Detalhes de jogos e blockchain funcionais.
- Market funcional: abrir anuncio, chat, edicao e escrow.
- Guild auctions funcional: ativo, warehouse, publish, historico.
- `api.js` continua exportando `api` compativel.
- Arquivos grandes tiveram reducao real de responsabilidade.
- Documentacao de transicao `db-*` vs legado foi atualizada.
- Evidencias visuais foram anexadas quando houve impacto de UI.
- Riscos pendentes foram registrados para P1/P2.

## Riscos Pendentes Esperados

- `gamesData` pode continuar necessario ate confirmacao de cobertura total pela API.
- `AdDetailsModal` pode precisar de mais de um PR interno se escrow estiver instavel.
- `GuildAuctionSection` pode precisar ser dividido em sub-PRs por tab se o diff ficar grande.
- Separar `api.js` pode revelar duplicacoes e nomes inconsistentes que devem ser corrigidos com cautela.

## Regra de Parada

Parar a sequencia e revisar o plano se qualquer uma destas condicoes ocorrer:

- Build falha por causa nao compreendida.
- Fluxo critico de compra/chat/escrow quebra.
- Fluxo critico de leilao perde dados ou executa acao no item errado.
- PR ultrapassa a fronteira planejada.
- Mudanca exige backend.
- Ha conflito com alteracoes locais que nao pertencem ao incremento.

