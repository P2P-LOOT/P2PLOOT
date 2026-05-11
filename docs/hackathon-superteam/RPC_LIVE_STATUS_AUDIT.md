# RPC live checkout audit

Branch alvo dos subrepos: `track/solana-rpc-live-checkout`

## Objetivo do incremento

Dar evidencia tecnica de que o P2P LOOT opera como dApp live/demo-live com RPC Solana configuravel, status de rede e link de explorer no checkout.

Esta track cobre o MVP compartilhado para:

- Eitherway/Solflare/QuickNode/Birdeye.
- RPC Fast.
- Superteam Brasil como demo guarda-chuva.

## Estado encontrado

Backend:

- `.env.example` ja define `SOLANA_RPC_URL`.
- `GET /api/web3/status` ja expunha rede, host RPC e readiness Web3.
- Eventos de escrow ja carregam assinaturas e, em modo devnet demo, `explorerUrl`.
- `scripts/check-web3-status.js` ja consultava `/api/web3/status`.

Frontend:

- `.env.example` ja define `VITE_SOLANA_RPC_URL` e `VITE_SOLANA_RPC_SUBSCRIPTIONS_URL`.
- `main.jsx` configura Privy/Solana com RPC e block explorer.
- O painel tecnico do checkout ja mostrava status devnet/Kora e link para Solana Explorer quando existe evento com assinatura.

## Ajuste aplicado

Backend:

- `/api/web3/status` agora executa healthcheck real do RPC com `getVersion()` e `getSlot()`.
- O payload `solana.rpcHealth` inclui:
  - `checked`;
  - `ok`;
  - `latencyMs`;
  - `slot`;
  - `version`;
  - `error`, quando falhar.
- `solana_rpc_health_failed` entra em `readiness.blockers` quando o RPC configurado falha.
- `npm run web3:status` passa a falhar tambem se `rpcHealth.ok` nao estiver verdadeiro quando o RPC esta configurado.

Frontend:

- O painel tecnico do checkout agora mostra um chip `RPC <host> <latencia>`.
- Quando o backend reporta falha, o chip mostra `RPC <host> falhou`.
- O link de explorer existente continua atrelado ao ultimo evento do escrow.

## Validacao executada

- Backend `git diff --check`: passou.
- Backend `node --check scripts/check-web3-status.js`: passou.
- Backend `npm run build`: passou.
- Frontend `git diff --check`: passou.
- Frontend `VITE_API_URL=http://localhost:6110 npm run build`: passou.
- Check vivo `API_BASE=http://localhost:6110 npm run web3:status`: passou com:
  - `solana.rpcHost: api.devnet.solana.com`;
  - `solana.rpcHealth.ok: true`;
  - `solana.rpcHealth.slot` preenchido;
  - `solana.rpcHealth.latencyMs` preenchido.

Avisos conhecidos:

- Frontend manteve avisos Rollup sobre anotacoes `/*#__PURE__*/` em dependencias `@privy-io/react-auth`/`ox`.
- Backend manteve aviso de fallback JS de `bigint`.

## Limites honestos

- Esta entrega nao adiciona Birdeye quote.
- Esta entrega nao muda a camada de wallet; ela evidencia o RPC usado pelo backend e o checkout.
- Solflare permanece como compatibilidade/instrucao via provider Solana/Privy ate validacao visual dedicada.

## Decisao de gate

A track esta em `implementing`: RPC health e visibilidade no checkout foram cobertos, mas ainda faltam evidencia visual do chip no checkout e decisao sobre Birdeye/Solflare antes de promocao.
