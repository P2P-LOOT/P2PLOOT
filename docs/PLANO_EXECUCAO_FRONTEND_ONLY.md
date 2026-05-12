# Plano de Execucao — Mudancas Somente de Frontend

Data: 08/05/2026  
Escopo: apenas `p2ploot-frontend` (sem mudancas de endpoint/contrato/permissao backend)

---

## 1) Objetivo

Executar as melhorias que dependem exclusivamente do frontend para:

- reduzir chamadas evitaveis;
- diminuir complexidade de manutencao;
- padronizar UX/feedback;
- preparar base para futuras melhorias de fluxo sem bloquear no backend.

---

## 2) Itens no escopo (frontend only)

1. Remover duplicacao de `getGames()` em `src/lib/api.js`.
2. Aplicar debounce no autocomplete de itens em `GuildAuctionSection`.
3. Padronizar consumo de CoinGecko (eliminar `fetch` direto em paginas).
4. Remover refetch desnecessario apos `sendMessage` no `AdDetailsModal`.
5. Reduzir acoplamento de componentes monoliticos (extracoes locais sem alterar fluxo backend).
6. Finalizar limpeza de `alert()/confirm()` residuais em fluxos criticos.

---

## 3) Plano por ondas

## Onda 0 — Baseline e preparo (0.5 dia)

### Entregas

- Congelar baseline de comportamento atual das telas criticas:
  - Player Market (chat + ad details)
  - Guilda (auction + management)
  - Tokens/Mercado
- Confirmar lista de arquivos-alvo e sequencia de PRs.

### Criterio de aceite

- Checklist de regressao manual definida por fluxo.
- Escopo fechado sem itens de backend.

---

## Onda 1 — Correcao de baixo risco e alto retorno (1 dia)

### Entregas

1. **API client**
  - Remover duplicacao de `getGames()` em `src/lib/api.js`.
2. **Chat**
  - Em `AdDetailsModal`, usar retorno do POST de mensagem para atualizar estado local sem `getMessages` imediato em toda interacao.
3. **Debounce**
  - Aplicar `useDebounce` no input de autocomplete em `GuildAuctionSection`.

### Arquivos principais

- `src/lib/api.js`
- `src/components/PlayerMarket/AdDetailsModal.jsx`
- `src/components/Guild/GuildAuctionSection.jsx`
- `src/hooks/usePerformance.js` (reuso)

### Criterio de aceite

- Nenhuma duplicacao de `getGames`.
- Menos requests em digitacao e envio de mensagem (verificacao no Network tab).
- Fluxos de chat e autocomplete continuam funcionais.

---

## Onda 2 — Padronizacao da camada de rede no frontend (1 a 1.5 dia)

### Entregas

1. Criar adapter frontend para CoinGecko (ex.: `src/lib/external/coingecko.js`).
2. Migrar `TokensPage` e `MercadoPage` para usar esse adapter.
3. Unificar tratamento de erro e parsing de resposta.

### Arquivos principais

- `src/components/Tokens/TokensPage.jsx`
- `src/components/Market/MercadoPage.jsx`
- `src/lib/external/coingecko.js` (novo)

### Criterio de aceite

- Nenhum `fetch(` direto em `TokensPage` e `MercadoPage`.
- Comportamento visual mantido.
- Erro de rede exibido com feedback consistente.

---

## Onda 3 — UX e feedback (1 a 1.5 dia)

### Entregas

1. Mapear e substituir `alert/confirm` residuais nos fluxos priorizados:
  - Guild auctions
  - Guild management/modals
  - Player market remanescente
2. Padronizar feedback assíncrono via `showToast` (loading/success/error).

### Arquivos principais (prioridade)

- `src/components/Guild/GuildAuctionSection.jsx`
- `src/components/Guild/GuildManagementPage.jsx`
- `src/components/Guild/modals/FieldConfigModal.jsx`
- `src/components/Guild/modals/MemberManagementModal.jsx`
- `src/components/PlayerMarket/`*

### Criterio de aceite

- Reducao significativa de uso de `alert(` e `confirm(` nos fluxos criticos.
- Operacoes sensiveis com feedback de loading e resultado.

---

## Onda 4 — Refatoracao estrutural frontend (3 a 4 dias)

### Entregas

1. Extrair hooks locais para reduzir acoplamento:
  - `useConversationFlow` (chat/ad details)
  - `useEscrowFlow` (acoes de escrow no modal)
  - `useAuctionBulkActions` / `useAuctionSearch` (guild auction)
2. Quebrar blocos utilitarios para diminuir tamanho dos componentes sem alterar regra.

### Arquivos alvo

- `src/components/PlayerMarket/AdDetailsModal.jsx`
- `src/components/Guild/GuildAuctionSection.jsx`

### Criterio de aceite

- Componentes com menos responsabilidades por arquivo.
- Sem mudanca funcional perceptivel para usuario.
- Regressao manual dos fluxos principais aprovada.

---

## Onda 5 — Qualidade e protecao de mudancas (1 dia)

### Entregas

1. Versionar e ativar ESLint no frontend.
2. Adicionar checks minimos para evitar regressao de padrao (DRY e usos proibidos de `alert/confirm` em areas definidas).
3. Revisar lints nas telas alteradas.

### Criterio de aceite

- `npm run lint` funcional no frontend.
- Principais arquivos alterados sem erros novos.

---

## 4) Sequencia sugerida de PRs

1. **PR-FE-01**: Onda 1 (duplicacao + debounce + chat roundtrip).
2. **PR-FE-02**: Onda 2 (adapter CoinGecko + migracao de paginas).
3. **PR-FE-03**: Onda 3 (limpeza de feedback UX e alert/confirm).
4. **PR-FE-04**: Onda 4 parte 1 (`AdDetailsModal`).
5. **PR-FE-05**: Onda 4 parte 2 (`GuildAuctionSection`).
6. **PR-FE-06**: Onda 5 (lint e hardening final).

---

## 5) Validacao por fluxo (checklist rapido)

### Player Market

- abrir anuncio
- iniciar conversa
- enviar mensagem
- editar anuncio
- denunciar anuncio

### Guilda

- buscar item por autocomplete
- publicar/cancelar leilao
- acoes em lote (delivery/delete history)
- configuracao de campos
- gestao de membro

### Tokens/Mercado

- alternar tabs
- trocar filtros
- tratar erro de API externa

---

## 6) Riscos e mitigacao

### Risco 1: regressao em chat

- Mitigacao: PR isolado + testes manuais de envio/inbox antes de merge.

### Risco 2: mudanca de comportamento em telas com muitos side effects

- Mitigacao: refatoracao por extracao incremental de hook (sem alterar contrato de props primeiro).

### Risco 3: variacao de UX ao remover `confirm`

- Mitigacao: padrao unico com toast de loading + estado de acao + mensagens claras.

---

## 7) Resultado esperado ao final

- Menos chamadas evitaveis no frontend.
- Fluxos mais previsiveis e com feedback padronizado.
- Menor custo de manutencao em componentes criticos.
- Base pronta para evolucoes que depois exigirao backend (realtime, ack de notificacao, batch API).