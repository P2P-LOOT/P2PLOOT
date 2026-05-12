# Relatorio de Investigacao: Grandes Blocos de Codigo no Frontend

Data: 8 de maio de 2026

Escopo analisado: `p2ploot-frontend/src`

Base de referencia:
- `RELATORIO_ARQUITETURA_FRONTEND.md`
- Leitura direta dos principais arquivos monoliticos atuais

## Objetivo

Identificar grandes blocos de codigo no front-end, explicar sua funcao atual e propor como modularizar cada bloco com baixo risco de regressao.

## Conclusao executiva

O principal problema nao e apenas tamanho de arquivo. O problema central e **concentracao de responsabilidades**:
- UI, regras de negocio, estado local, persistencia (`localStorage`) e chamadas de API no mesmo componente.
- Paginas com muitas abas/modais que acumulam orquestracao de fluxo e renderizacao.
- Cliente API unico (`api.js`) concentrando contratos de varios dominios.

Isso aumenta:
- custo de manutencao;
- risco de regressao em mudancas pequenas;
- conflito entre features paralelas;
- dificuldade de revisar e testar.

## Blocos grandes mapeados (estado atual)

| Arquivo | Linhas atuais | Funcao principal | Prioridade |
| --- | ---: | --- | --- |
| `src/components/Guild/GuildAuctionSection.jsx` | 2404 | Fluxo completo de leiloes (cadastro, importacao, publicacao, lance, historico) | P0 |
| `src/components/Web3Games/Web3GamesPage.jsx` | 1737 | Home de jogos + detalhes + blockchain + modais no mesmo arquivo | P0 |
| `src/UserProfilePage.jsx` | 1606 | Superpagina com abas de perfil/guild/ads/creditos/suporte | P0 |
| `src/components/PlayerMarket/AdDetailsModal.jsx` | 1483 | Chat, handshake, escrow Web3, denuncia, edicao e galeria em um modal | P0 |
| `src/components/Guild/GuildManagementPage.jsx` | 1480 | Pagina mestre da guilda com dashboard, membros, pilotos e modais | P1 |
| `src/components/Guild/GuildDkpModals.jsx` | 1348 | Varios modais DKP com regras compartilhadas no mesmo arquivo | P1 |
| `src/components/Admin/AdminDashboard.jsx` | 1290 | Dashboard admin + modais internos de bans/moderadores/punicao | P1 |
| `src/lib/api.js` | 860 | Gateway unico de API para varios dominios | P0 |
| `src/components/common/Navbar.jsx` | 758 | Navbar + notificacoes + polling + menus globais | P2 |
| `src/components/Guild/modals/MemberManagementModal.jsx` | 757 | Perfil + penalidade + historico + compartilhamento em modal unico | P2 |

## Analise por bloco e modularizacao proposta

### 1) `GuildAuctionSection.jsx` (P0)

**Funcao atual**
- Gerencia tabs de leilao (`active`, `upcoming`, `closed`, `warehouse`).
- Faz CRUD de itens, importacao CSV, publicacao em lote, lances, historico e entrega.
- Mantem filtros, favoritos, modo de visualizacao, auto refresh e timers.

**Problema**
- Estado de UI + regras de negocio + persistencia local + API + renderizacao densa no mesmo modulo.
- Alto acoplamento entre partes independentes (ex.: filtro de historico vs criacao/publicacao).

**Modularizacao sugerida**
- Hooks:
  - `useGuildAuctionsData`
  - `useAuctionPreferences`
  - `useAuctionActions`
- Subcomponentes:
  - `AuctionTabs`
  - `AuctionToolbar`
  - `WarehouseView`
  - `AuctionHistoryView`
  - `ActiveAuctionsView`
  - `PublishAuctionModal`
  - `ImportWarehouseModal`

---

### 2) `Web3GamesPage.jsx` (P0)

**Funcao atual**
- Concentra Home, `GameDetailsPage`, `BlockchainDetailsPage`, `GameCard`, `EditGameModal` e comentarios.
- Faz fetch de jogos/comentarios e controle de voto/favoritos.

**Problema**
- Multipla responsabilidade no mesmo arquivo (varias paginas + componentes + modais).
- Regras de permissao e logica de comentarios/votos espalhadas e parcialmente duplicadas.

**Modularizacao sugerida**
- Separar por arquivo:
  - `Web3GamesHome.jsx`
  - `GameDetailsPage.jsx`
  - `BlockchainDetailsPage.jsx`
  - `GameCard.jsx`
  - `EditGameModal.jsx`
- Hooks:
  - `useGamesCatalog`
  - `useGameVotes`
  - `useGameComments`
- Ajuste de rotas para lazy load por arquivo (evitar named export de arquivo monolitico).

---

### 3) `UserProfilePage.jsx` (P0)

**Funcao atual**
- Abas: `settings`, `guilds`, `characters`, `ads`, `credits`, `support`.
- Orquestra varios modais e fetches condicionais por aba.

**Problema**
- Grande volume de JSX + regras de negocio + fetch por aba no mesmo componente.
- Dificulta mudanca localizada (ex.: mexer em creditos sem risco em ads).

**Modularizacao sugerida**
- Estrutura de pagina:
  - `UserProfileLayout`
  - `ProfileSettingsTab`
  - `ProfileGuildsTab`
  - `ProfileCharactersTab`
  - `ProfileAdsTab`
  - `ProfileCreditsTab`
  - `ProfileSupportTab`
- Hooks por dominio:
  - `useProfileSettings`
  - `useProfileGuilds`
  - `useProfileCharacters`
  - `useProfileAds`
  - `useProfileCredits`

---

### 4) `AdDetailsModal.jsx` (P0)

**Funcao atual**
- Chat comprador/vendedor.
- Handshake de conclusao de negocio.
- Fluxo de escrow Web3.
- Denuncia, edicao, upload de imagem e lightbox.

**Problema**
- Estados de conversa, pagamento e edicao competindo no mesmo ciclo de vida.
- Muitos efeitos e caminhos condicionais no mesmo modal.

**Modularizacao sugerida**
- Componentes:
  - `AdDetailsView`
  - `AdChatPanel`
  - `TradeHandshakePanel`
  - `EscrowPanel`
  - `EditAdPanel`
- Hooks:
  - `useAdConversation`
  - `useTradeHandshake`
  - `useEscrowFlow`
  - `useAdEditForm`

---

### 5) `api.js` (P0)

**Funcao atual**
- Cliente unico para auth, profile, mensagens, guildas, DKP, leiloes, jogos, market, creditos, suporte, eventos e admin.

**Problema**
- Arquivo unico para contratos de dominios diferentes.
- Alto risco de conflito em PRs paralelos.
- Ownership funcional difuso.

**Modularizacao sugerida**
- Criar pasta `src/lib/api/` com:
  - `request.js`
  - `authApi.js`
  - `profileApi.js`
  - `guildApi.js`
  - `dkpApi.js`
  - `auctionApi.js`
  - `gamesApi.js`
  - `marketApi.js`
  - `creditsApi.js`
  - `supportApi.js`
  - `eventsApi.js`
  - `adminApi.js`
  - `index.js` (barrel de compatibilidade)

---

### 6) `GuildManagementPage.jsx` (P1)

**Funcao atual**
- Tela principal de gestao de guilda: membros, historico, pilotos e modais de configuracao.

**Problema**
- Container com muitas responsabilidades de dados, permissoes e renderizacao.

**Modularizacao sugerida**
- `GuildManagementContainer` (orquestrador)
- `GuildOverviewTabs`
- `GuildMembersPanel`
- `GuildPilotsPanel`
- hooks:
  - `useGuildManagementData`
  - `useGuildPermissions`
  - `useGuildPilotState`

---

### 7) `GuildDkpModals.jsx` (P1)

**Funcao atual**
- Agrupa 4 modais complexos com regras compartilhadas de score/matching.

**Problema**
- Repeticao de logica e aumento de custo de manutencao.

**Modularizacao sugerida**
- Separar modais por arquivo:
  - `DkpSettingsModal.jsx`
  - `DkpConfigModal.jsx`
  - `DkpDistributionModal.jsx`
  - `AddMembersToEventModal.jsx`
- Hooks compartilhados:
  - `useDkpScoreCalculator`
  - `useDkpMemberMatcher`
  - `useDkpEventForm`

---

### 8) `AdminDashboard.jsx` (P1)

**Funcao atual**
- Dashboard principal + modais internos de punicao, banimento e moderadores.

**Problema**
- Mistura responsabilidades de tela principal com modais complexos.

**Modularizacao sugerida**
- `AdminDashboardPage`
- `AdminReportsPanel`
- `PunishmentModal`
- `BannedUsersModal`
- `ModeratorsModal`
- hooks:
  - `useAdminDashboardData`
  - `useModeratorsManagement`
  - `useBannedUsersManagement`

---

### 9) `Navbar.jsx` (P2)

**Funcao atual**
- Navbar global com dropdowns, notificacoes e polling.

**Problema**
- Comportamento de notificacao acoplado com renderizacao.

**Modularizacao sugerida**
- Hooks:
  - `useNavbarNotifications`
  - `useNavbarDropdownGuards`
- Componente `Navbar` focado em composicao visual.

---

### 10) `MemberManagementModal.jsx` (P2)

**Funcao atual**
- Perfil, penalidades, ajuste manual e historico em um unico modal.

**Problema**
- Fluxos funcionais distintos compartilhando estado local grande.

**Modularizacao sugerida**
- Tabs/componentes:
  - `MemberProfileTab`
  - `MemberDkpTab`
  - `MemberHistoryTab`
- Hook:
  - `useMemberDkpActions`

## Sequencia recomendada (incremental)

### Fase 0 - Estabilizacao
- Corrigir bugs ativos e inconsistencias mais claras.
- Congelar contratos sensiveis antes de extracoes.

### Fase 1 - Extracao de dados (baixo risco visual)
- Mover `fetch/loading/error` para hooks.
- Manter JSX proximo do atual para reduzir regressao.

### Fase 2 - Quebra de UI por dominio
- Extrair views/tabs/modais para arquivos menores.
- Introduzir containers de pagina mais finos.

### Fase 3 - Modularizacao de API
- Extrair `api.js` para modulos por dominio com barrel de compatibilidade.
- Migrar consumidores gradualmente.

### Fase 4 - Consolidacao
- Limpar duplicacoes e legado remanescente.
- Padronizar organizacao final de pastas por feature.

## Criterios de sucesso da modularizacao

- Nenhum bloco de pagina principal acima de ~600-800 linhas.
- Hooks responsaveis por dados e side effects; componentes focados em renderizacao.
- Cliente API organizado por dominio.
- PRs menores e mais revisaveis por feature.
- Menor taxa de regressao em mudancas locais.

## Proximos passos praticos

1. Iniciar por `api.js` (estrutura modular com compatibilidade).
2. Extrair hooks de `GuildAuctionSection` e `UserProfilePage`.
3. Quebrar `Web3GamesPage` em arquivos de rota separados.
4. Fatiar `AdDetailsModal` por paines funcionais.
5. Separar `GuildDkpModals` e `AdminDashboard` em modulos de dominio.

