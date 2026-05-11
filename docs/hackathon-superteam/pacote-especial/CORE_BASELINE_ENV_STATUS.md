# Superteam core baseline/env/status

Branch alvo dos subrepos: `track/superteam-core`

## Objetivo do incremento

Registrar o baseline compartilhado que sustenta as tracks Superteam antes de mover novas features para `teste`.

Este incremento nao adiciona codigo de produto. Ele separa o que ja existe no core, o que ficou especifico da track Tether e o que precisa virar incremento compartilhado antes de Cloak, Superteam Brasil, RPC/Fast ou outras tracks dependerem disso.

## Baseline atual do backend

Arquivos/areas relevantes:

- `src/app/api/web3/status/route.ts`
- `src/app/api/web3/wallets/route.ts`
- `src/app/api/web3/wallets/devnet-demo/route.ts`
- `src/app/api/web3/escrows/route.ts`
- `src/app/api/web3/escrows/[id]/route.ts`
- `src/app/api/web3/privacy/status/route.ts`
- `src/lib/solana-rpc.ts`
- `src/lib/solana-devnet-demo.ts`
- `src/lib/solana-anchor-escrow-demo.ts`
- `src/lib/cloak-privacy.ts`
- `scripts/check-web3-status.js`
- `scripts/smoke-web3.js`
- `scripts/smoke-web3-devnet.js`
- `scripts/seed-web3-smoke-data.js`

Capacidades ja presentes:

- Health/status Web3 com RPC, devnet demo, Privy, Kora e taxa de plataforma.
- Escrow por conversa de anuncio com estados principais e eventos.
- Wallet Web3 vinculada ao perfil e wallet demo devnet.
- Fallback local documentado para demo devnet quando nao houver transacao real.
- Auto-release de escrow elegivel.
- Metadata inicial de privacidade/Cloak no backend.

Env vars principais do backend:

- `DATABASE_URL`
- `JWT_SECRET`, `JWT_REFRESH_SECRET`, `NEXTAUTH_SECRET`
- `NEXT_PUBLIC_FRONTEND_URL`
- `PRIVY_APP_ID`, `PRIVY_APP_SECRET`
- `SOLANA_NETWORK`, `SOLANA_RPC_URL`, `SOLANA_ESCROW_PROGRAM_ID`
- `SOLANA_CHECK_PROGRAM_DEPLOYMENT`, `SOLANA_VALIDATE_TX_SIGNATURES`
- `PLATFORM_FEE_BPS`
- `SOLANA_DEVNET_DEMO_ENABLED`, `SOLANA_DEVNET_DEMO_FALLBACK_TO_LOCAL`
- `SOLANA_ANCHOR_ESCROW_DEMO_ENABLED`, `SOLANA_ANCHOR_DEMO_TOKEN_DECIMALS`
- `KORA_RPC_URL`, `KORA_NETWORK`, `KORA_FEE_PAYER_ADDRESS`

## Baseline atual do frontend

Arquivos/areas relevantes:

- `src/components/PlayerMarket/PlayerMarketPage.jsx`
- `src/components/PlayerMarket/AdDetailsModal.jsx`
- `src/components/PlayerMarket/CreateAdModal.jsx`
- `src/lib/api.js`
- `src/main.jsx`
- `.env.example`

Capacidades ja presentes:

- Marketplace lista anuncios ativos vindos do backend.
- Modal de anuncio concentra chat, compra/negociacao, escrow, wallet, disputa e detalhes tecnicos.
- Checkout Web3 usa backend para wallet, status, criacao e atualizacao de escrow.
- Detalhe tecnico mostra wallet, status, readiness devnet/Kora, valor, fee e ultimo evento quando existe escrow.
- Privy/Solana/Kora estao parametrizados por envs `VITE_*`.

Env vars principais do frontend:

- `VITE_API_URL`
- `VITE_ADMIN_EMAIL`
- `VITE_SOLANA_NETWORK`
- `VITE_SOLANA_RPC_URL`
- `VITE_SOLANA_RPC_SUBSCRIPTIONS_URL`
- `VITE_SOLANA_ESCROW_PROGRAM_ID`
- `VITE_SOLANA_DEVNET_DEMO_ENABLED`
- `VITE_KORA_RPC_URL`
- `VITE_PRIVY_APP_ID`

## Lacunas de core

- `AdDetailsModal.jsx` ainda e o orquestrador principal com 2597 linhas; extrair componentes/hooks reduz risco antes de somar Cloak, Jupiter e novas tracks.
- No core base, `CreateAdModal.jsx` e edicao do anuncio ainda listam `USDC`, `SOL`, `TROCA`, `N/A`; suporte `USDT` esta estabilizado na branch `track/tether-usdt-escrow`.
- No core base, resolucao de mint por stablecoin/rede ainda nao esta consolidada; isso esta na branch Tether.
- O seed Web3 parametrizado por moeda esta na branch Tether e deve ser avaliado para promocao ao core se outras tracks usarem demo stablecoin.
- Evidencias visuais existem para Tether, mas o core ainda precisa de uma evidencia neutra de checkout/escrow compartilhado.

## Proximos incrementos recomendados

1. `track-inc/superteam-core/01-env-matrix-doc`
   - Consolidar matriz de envs backend/frontend por ambiente.
   - Definir quais envs sao obrigatorias, opcionais e apenas demo.
2. `track-inc/superteam-core/02-escrow-formatting-utils`
   - Extrair parsing/formatacao de moeda, valor, status e explorer para util compartilhado.
   - Reduzir duplicacao antes de Cloak/Jupiter.
3. `track-inc/superteam-core/03-checkout-panel-extraction`
   - Extrair painel Web3/checkout do `AdDetailsModal.jsx`.
   - Manter comportamento atual com build e evidencia visual.

## Gate para promover core para `teste`

- Builds frontend/backend passando na branch `track/superteam-core`.
- Matriz de envs aprovada.
- Nenhuma feature especifica de track misturada sem flag ou decisao explicita.
- Smoke Web3 base executado ou documentado como pendente por dependencia local.
- Evidencia visual neutra de checkout/escrow compartilhado.
