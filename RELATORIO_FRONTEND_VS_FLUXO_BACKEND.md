# Investigacao: Problemas apenas de Frontend vs Problemas de Fluxo (Frontend + Backend)

Data: 08/05/2026  
Escopo: `p2ploot-frontend/src` e `p2ploot-backend/src/app/api`

---

## 1) Criterio de classificacao

### A. Problema apenas de Frontend
Pode ser corrigido sem alterar endpoint, contrato, permissao ou capacidade do backend.

### B. Problema de Fluxo (Frontend + Backend)
Depende de alinhamento de contrato API, capacidade de backend (ex.: realtime), modelo de permissao ou desenho de endpoints.

---

## 2) Problemas apenas de Frontend

## 2.1 Duplicacao de metodo na camada cliente API
- Evidencia: `p2ploot-frontend/src/lib/api.js` possui `getGames()` declarado duas vezes.
- Impacto: risco de manutencao e comportamento sobrescrito sem clareza.
- Dono principal: Frontend.

## 2.2 Autocomplete sem debounce apesar de hook existente
- Evidencia:
  - `p2ploot-frontend/src/components/Guild/GuildAuctionSection.jsx` chama `api.getGameItems(...)` a cada digitacao.
  - `p2ploot-frontend/src/hooks/usePerformance.js` ja possui `useDebounce`.
- Impacto: chamadas desnecessarias e UX mais instavel em digitacao rapida.
- Dono principal: Frontend.

## 2.3 Inconsistencia de camada de rede no frontend
- Evidencia:
  - `p2ploot-frontend/src/components/Tokens/TokensPage.jsx` usa `fetch` direto.
  - `p2ploot-frontend/src/components/Market/MercadoPage.jsx` usa `fetch` direto.
- Impacto: tratamento de erro, retry e observabilidade ficam fragmentados.
- Dono principal: Frontend.

## 2.4 Refetch desnecessario apos envio de mensagem
- Evidencia:
  - `p2ploot-frontend/src/components/PlayerMarket/AdDetailsModal.jsx` chama `api.sendMessage(...)` e em seguida `api.getMessages(...)`.
  - `p2ploot-backend/src/app/api/conversations/[id]/messages/route.ts` ja retorna a mensagem criada no POST.
- Impacto: roundtrip extra evitavel no frontend.
- Dono principal: Frontend (otimizacao local possivel sem mudar backend).

## 2.5 Complexidade e mistura de responsabilidades em componentes grandes
- Evidencia: `AdDetailsModal.jsx`, `GuildAuctionSection.jsx`, `GuildManagementPage.jsx`, `Web3GamesPage.jsx`.
- Impacto: baixo isolamento de mudanca (SRP/SoC), maior risco de regressao.
- Dono principal: Frontend.

## 2.6 Residuos de UX e padrao de feedback inconsistente
- Evidencia: ainda ha uso de `alert()/confirm()` em fluxos de guilda/leilao.
- Impacto: UX inconsistente e menos previsivel.
- Dono principal: Frontend.

---

## 3) Problemas de Fluxo (Frontend + Backend)

## 3.1 Ausencia de realtime no backend forcando polling no frontend
- Evidencia:
  - Frontend:
    - `p2ploot-frontend/src/components/PlayerMarket/AdDetailsModal.jsx` (poll de chat a cada 5s).
    - `p2ploot-frontend/src/components/Guild/GuildAuctionSection.jsx` (comentario de realtime desativado durante migracao API).
  - Backend:
    - nao ha implementacao de websocket/SSE para chat/notificacoes nas rotas API atuais.
- Impacto: maior carga de rede, latencia de sincronizacao e UX menos fluida.
- Dono: Compartilhado (Backend + Frontend).

## 3.2 Notificacoes sem estado de leitura/dispensa persistido no servidor
- Evidencia:
  - Backend:
    - `p2ploot-backend/src/app/api/notifications/route.ts` expoe GET agregador.
    - `p2ploot-backend/src/app/api/admin/notifications/route.ts` expoe GET de contagens.
    - nao ha endpoint dedicado para ack/read/dismiss.
  - Frontend:
    - `p2ploot-frontend/src/components/common/Navbar.jsx` persiste dismiss em `localStorage`.
- Impacto: regra de "lido/dispensado" fica local e pode divergir entre dispositivos/sessoes.
- Dono: Compartilhado (modelo de estado e contrato API).

## 3.3 Contrato de permissao desalinhado para notificacoes admin
- Evidencia:
  - Frontend:
    - `Navbar.jsx` considera `admin` OU `moderator` para buscar notificacoes admin.
  - Backend:
    - `admin/notifications/route.ts` permite apenas admin (retorna 403 para nao-admin).
- Impacto: chamadas rejeitadas recorrentes e necessidade de tratamento defensivo no frontend.
- Dono: Compartilhado (contrato de autorizacao).

## 3.4 Contrato de dados inconsistente (camelCase + snake_case)
- Evidencia:
  - Backend:
    - `p2ploot-backend/src/app/api/conversations/route.ts` e `ads/route.ts` retornam chaves duplicadas em camel e snake para compatibilidade.
    - `p2ploot-backend/src/app/api/guilds/[id]/route.ts` mistura campos e aliases.
  - Frontend:
    - `GuildManagementPage.jsx`, `MemberManagementModal.jsx`, `FieldConfigModal.jsx`, `useGames.js` fazem normalizacao defensiva extensa.
- Impacto: maior custo cognitivo e superficie de bug em mapeamento.
- Dono: Compartilhado (padronizacao de contrato).

## 3.5 Fluxos em lote sem endpoint batch dedicado
- Evidencia:
  - Frontend:
    - `GuildAuctionSection.jsx` usa `Promise.all` para delivery/history em lote.
  - Backend:
    - endpoints sao item-a-item:
      - `guilds/[id]/auctions/[auctionId]/delivery/route.ts`
      - `guilds/[id]/auctions/[auctionId]/route.ts` (delete history)
- Impacto: rajadas de requests, maior chance de falha parcial e custo de retry.
- Dono: Compartilhado (API batch + adaptacao front).

## 3.6 Fluxo de conversa exige encadeamento de chamadas em certas transicoes
- Evidencia:
  - Backend:
    - `ads/[id]/conversation/route.ts` (POST) retorna `conversationId` e `messageId`.
  - Frontend:
    - `AdDetailsModal.jsx` inicia conversa e depois recarrega mensagens.
- Impacto: custo extra em abertura de conversa, com logica de encadeamento no componente.
- Dono: Compartilhado (resposta mais rica no backend ou consolidacao de fluxo no frontend).

## 3.7 Dados de tela de guilda fragmentados em multiplos endpoints
- Evidencia:
  - Frontend:
    - `GuildManagementPage.jsx` faz `getGuild(...)` + `getPresenceStats(...)`.
  - Backend:
    - `guilds/[id]/route.ts` e `guilds/[id]/dkp/stats/presence/route.ts` separados.
- Impacto: mais roundtrips para render inicial e maior acoplamento da tela a composicao de chamadas.
- Dono: Compartilhado (decisao de agregacao de leitura).

---

## 4) Sumario de ownership

- **Somente Frontend (acao direta):** 6 itens principais.
- **Fluxo Frontend + Backend (acao coordenada):** 7 itens principais.

Leitura pratica:
- Frontend tem quick wins relevantes (debounce, deduplicacao, consolidacao de camada API, refetch desnecessario).
- Gargalos estruturais de fluxo dependem de contrato e capacidade backend (realtime, notificacao com estado, padrao de payload, batch endpoints).

---

## 5) Ordem recomendada

1. **Quick wins Frontend (1 sprint):**
   - remover duplicacoes no client API;
   - aplicar debounce no autocomplete;
   - padronizar `fetch` externo em adapter unico;
   - remover refetch redundante apos `sendMessage`.

2. **Fluxo compartilhado (2-3 sprints):**
   - definir contrato unico de payload (preferencia camelCase);
   - definir politica de notificacao (read/dismiss server-side);
   - alinhar permissao de moderador em notificacoes admin;
   - introduzir endpoints batch para operacoes de leilao.

3. **Evolucao arquitetural:**
   - avaliar canal realtime (chat/notificacoes) para substituir polling agressivo.
