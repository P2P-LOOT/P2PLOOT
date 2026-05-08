# Investigação das ~180 Chamadas de API no Frontend

Data: 08/05/2026  
Base principal: `RELATORIO_ARQUITETURA_FRONTEND.md`  
Escopo técnico: `p2ploot-frontend/src`

---

## 1) Objetivo da investigação

Investigar:

1. quais são as chamadas de API no frontend;
2. o que elas fazem;
3. por que há tantas chamadas;
4. se há duplicação e/ou más práticas de código sob ótica de clean code.

---

## 2) Método de análise

- Leitura do relatório-base de arquitetura (`RELATORIO_ARQUITETURA_FRONTEND.md`), que aponta `181` ocorrências de `api.`.
- Revarredura do frontend com foco em invocações reais de método:
  - `api.` (ocorrências amplas): volume maior por incluir comentários/strings/casos não executáveis.
  - `api.<metodo>(...)` (invocação direta): recorte mais fiel das chamadas no código.
- Mapeamento de concentração por arquivo e por domínio funcional.
- Revisão qualitativa dos arquivos críticos para identificar duplicação e violações de SRP/DRY/SoC.

Observação sobre "manuais de clean code":

- Não foi encontrado manual interno explícito de clean code no repositório.
- A avaliação foi feita com base em princípios consolidados: **SRP**, **DRY**, **Separation of Concerns**, **coesão alta / baixo acoplamento**, **evitar código morto**, **consistência de abstrações**.

---

## 3) Quais são as chamadas e o que elas fazem

As chamadas se concentram em 10 domínios de negócio. O frontend usa mais de 100 métodos distintos da fachada `api`.

### 3.1 Autenticação e sessão

- `getProfile`, `loginWithEmail`, `loginWithPrivy`, `getDiscordAuthUrl`.
- Função: login/autenticação e bootstrap da sessão do usuário.

### 3.2 Perfil, crédito e social

- `updateProfile`, `getPublicProfileByUsername`, `getMyAds`, `getMyGuilds`.
- `getCreditRequests`, `createCreditRequest`, `updateCreditRequest`, `getCreditRequest`, `sendCreditRequestMessage`, `getCreditTransactions`.
- `getMemberShares`, `addMemberShare`, `removeMemberShare`.
- Função: dados de perfil, solicitações de crédito e compartilhamento de personagem.

### 3.3 Mensagens e negociação entre usuários

- `getConversations`, `getAdConversations`, `getMessages`, `startConversation`, `sendMessage`, `deleteConversation`, `confirmTrade`.
- Função: inbox, chat de negociação e confirmação de transação.

### 3.4 Anúncios (marketplace)

- `getAds`, `getAd`, `createAd`, `updateAd`, `deleteAd`, `renewAd`, `reportAd`.
- Função: listagem/CRUD de anúncios e denúncia.

### 3.5 Web3/Escrow

- `getWeb3Wallets`, `getWeb3Status`, `linkWeb3Wallet`, `createDevnetDemoWallet`.
- `getEscrows`, `createEscrow`, `updateEscrow`.
- Função: carteira e fluxo de escrow para compra/venda.

### 3.6 Guildas e membros

- `getGuilds`, `getGuild`, `createGuild`, `updateGuild`, `deleteGuild`.
- `joinGuild`, `leaveGuild`, `transferLeadership`, `reactivateGuild`.
- `updateMember`, `kickMember`, `approvePilot`, `rejectPilot`, `processRequest`.
- `createCustomField`, `updateCustomField`, `deleteCustomField`, `reorderFields`.
- Função: gestão completa de guilda e governança interna.

### 3.7 DKP (pontuação/regras de guilda)

- `getDkpHistory`, `distributeDkp`, `revokeDkpEntry`, `getPresenceStats`, `applyManualDecay`.
- `createDkpEvent`, `updateDkpEvent`, `deleteDkpEvent`.
- Função: histórico, distribuição e regras de pontos.

### 3.8 Leilões/favoritos/sorteio

- `getAuctions`, `placeBid`, `upsertAuctionItem`, `getGameItems`, `publishAuctions`.
- `cancelAuction`, `startAuction`, `deleteAuctionHistory`, `importWarehouseItems`, `toggleAuctionDelivery`.
- `getFavorites`, `toggleFavorite`, `createGiveaway`.
- Função: lifecycle completo de itens/leilões e sorteios.

### 3.9 Jogos e eventos

- `getGames`, `getGame`, `createGame`, `updateGame`, `deleteGame`.
- `getGameComments`, `postGameComment`, `voteGameComment`, `deleteGameComment`.
- `getGameVotes`, `voteGame`.
- `getEvents`, `createEvent`, `updateEvent`, `deleteEvent`.
- Função: catálogo de jogos, comunidade (comentários/votos) e eventos.

### 3.10 Admin/moderação e suporte

- `getReports`, `updateReportStatus`, `moderateReport`.
- `getPendingApprovals`, `approveContent`, `rejectContent`.
- `searchUsers`, `getBannedUsers`, `banUser`, `unbanUser`.
- `getModerators`, `addModerator`, `updateModerator`, `removeModerator`.
- `getAdminNotifications`, `getUserNotifications`.
- `getMyTickets`, `createTicket`, `getTicket`, `updateTicketStatus`, `sendTicketMessage`, `getAdminTickets`.
- Função: operação de moderação, antifraude, tickets e observabilidade operacional.

---

## 4) Onde as chamadas estão concentradas

Maiores concentrações de `api.<metodo>(...)` no frontend:

1. `src/components/PlayerMarket/AdDetailsModal.jsx` -> 29
2. `src/components/Admin/AdminDashboard.jsx` -> 17
3. `src/components/Guild/GuildAuctionSection.jsx` -> 16
4. `src/components/Guild/GuildManagementPage.jsx` -> 15
5. `src/components/Web3Games/Web3GamesPage.jsx` -> 12
6. `src/UserProfilePage.jsx` -> 11

Leitura arquitetural:

- O volume está fortemente concentrado em poucos componentes grandes.
- Esses componentes misturam renderização + regra de negócio + orquestração de API.

---

## 5) Por que são tantas chamadas

### 5.1 Escopo funcional amplo (motivo legítimo)

A aplicação cobre muitos domínios simultaneamente (marketplace, guilda, DKP, admin, tickets, web3, eventos, jogos), o que naturalmente eleva o volume de endpoints e chamadas.

### 5.2 Orquestração de dados dentro de componentes monolíticos

Arquivos grandes concentram múltiplos fluxos e, por consequência, acumulam muitos `fetch`/`refetch`.

### 5.3 Polling frequente

- `src/components/Messages/MessagesInbox.jsx`: polling de conversas a cada 10s.
- `src/components/PlayerMarket/AdDetailsModal.jsx`: polling de mensagens a cada 5s.
- `src/components/common/Navbar.jsx`: polling admin (30s) + usuário (45s).

Isso aumenta volume de tráfego mesmo sem ação explícita do usuário.

### 5.4 Padrão de refetch pós-mutação

Após várias ações de escrita, a UI recarrega listas completas em vez de atualizar cache local pontualmente (custo maior de rede).

### 5.5 Operações em lote via N requisições

Em `GuildAuctionSection`, operações bulk usam `Promise.all` com uma request por item (pode gerar rajadas grandes).

### 5.6 Falta de debounce em pontos com input dinâmico

`GuildAuctionSection` chama `api.getGameItems(...)` durante digitação sem debounce, embora exista `useDebounce` no projeto (`src/hooks/usePerformance.js`) sem uso.

---

## 6) Duplicações e más práticas (clean code)

## Achados críticos/altos

### A) Duplicação direta de método na camada API (DRY violado)

- Arquivo: `src/lib/api.js`
- Método `getGames()` declarado duas vezes.
- Risco: manutenção confusa, sobrescrita silenciosa e comportamento inesperado em refactors.

### B) Duplicação de fluxo de chat no mesmo componente (DRY violado)

- Arquivo: `src/components/PlayerMarket/AdDetailsModal.jsx`
- Blocos parecidos de:
  - `startConversation -> getMessages`
  - `sendMessage -> getMessages`
- Risco: divergência de comportamento e bugs por alteração parcial.

### C) Requisição repetida com mesma assinatura em um único modal (DRY/coesão)

- Arquivo: `src/components/Guild/modals/MemberManagementModal.jsx`
- `api.getDkpHistory(guildId, { memberId, limit: 100 })` aparece em múltiplos fluxos próximos.
- Risco: duplicação de regra, maior acoplamento local e desperdício de requests.

### D) Redundância de carregamento de presença

- Arquivo: `src/components/Guild/GuildManagementPage.jsx`
- `api.getPresenceStats(...)` é chamado no fetch principal e em `useEffect` adicional.
- Risco: refetch duplicado e complexidade de estado.

## Achados médios

### E) Inconsistência de abstração de rede

- `src/components/Tokens/TokensPage.jsx` e `src/components/Market/MercadoPage.jsx` usam `fetch` direto para CoinGecko.
- Enquanto isso, o restante do app usa `api`.
- Risco: tratamento de erro/cache/retry/logging fragmentado.

### F) Código morto / API parcialmente não usada

Há métodos definidos em `api.js` sem uso no frontend atual (ex.: `loginWithWallet`, `loginWithDiscord`, `voteTrust`, `linkWallet`, `getWeb3PrivacyStatus`, `getEscrow`, `getGiveaways`, `getEvent`, `searchUserExact`).

Risco: aumenta superfície cognitiva e dificulta manutenção.

### G) SRP violado em componentes muito grandes

Componentes críticos acumulam responsabilidades de UI + regras + integração + normalização de dados em um mesmo arquivo, elevando custo de teste e revisão.

---

## 7) Conclusão objetiva

Sim, existe um volume alto de chamadas de API, mas ele é parcialmente justificável pelo escopo amplo da plataforma.  
O problema principal não é apenas "quantidade", e sim **como** elas são orquestradas:

- concentração em componentes monolíticos;
- polling agressivo;
- duplicação de fluxos e métodos;
- abstração de rede inconsistente;
- falta de estratégia de cache/debounce.

Em termos de clean code, os pontos mais relevantes são violações de **DRY**, **SRP** e **Separation of Concerns**.

---

## 8) Recomendações priorizadas

1. **Corrigir duplicação em `api.js`** (remover segunda definição de `getGames`).
2. **Extrair fluxo de chat de `AdDetailsModal` para hook/service** (`useConversation`), eliminando blocos duplicados.
3. **Aplicar debounce** em autocomplete (`getGameItems`) com o hook já existente.
4. **Padronizar camada de rede** (evitar `fetch` direto em componentes).
5. **Reduzir polling cego** (usar foco da aba, backoff, ou eventos/websocket quando possível).
6. **Introduzir cache de dados** (ex.: React Query/SWR) para reduzir refetch total pós-mutação.
7. **Fatiar componentes críticos** (`AdDetailsModal`, `GuildAuctionSection`, `GuildManagementPage`, `Web3GamesPage`) em hooks e subcomponentes.