# Superteam track status

Data de inicio da aplicacao: 2026-05-10

## Estado operacional

O fluxo de governanca foi iniciado com branches especiais locais criadas nos subrepos de produto:

- `p2ploot-frontend`
- `p2ploot-backend`

As branches foram criadas a partir de `origin/teste`, conforme o plano de governanca. Ainda nao foram enviadas para o remoto neste passo.

## Branches especiais criadas localmente

| Track | Frontend | Backend | Status |
| --- | --- | --- | --- |
| Core compartilhado | `track/superteam-core` | `track/superteam-core` | criada localmente |
| Tether | `track/tether-usdt-escrow` | `track/tether-usdt-escrow` | criada localmente |
| Cloak | `track/cloak-private-checkout` | `track/cloak-private-checkout` | criada localmente |
| Superteam Brasil | `track/superteam-brasil-demo` | `track/superteam-brasil-demo` | criada localmente |

## Ordem inicial de aplicacao

1. `track/superteam-core`
   - Consolidar baseline comum de checkout, escrow, RPC, envs e evidencia.
   - Separar o que e produto reutilizavel do que e demo de track.
2. `track/tether-usdt-escrow`
   - Garantir que USDT esteja consistente em anuncio, checkout, escrow e demo.
   - Validar se o backend ja aceita `currencySymbol: USDT` sem mudanca de banco.
3. `track/cloak-private-checkout`
   - Reaproveitar o baseline de escrow.
   - Garantir metadata minima e mascaramento antes de prometer integracao profunda.
4. `track/superteam-brasil-demo`
   - Usar como pacote de demo e submissao em PT-BR.
   - Evitar misturar polimento de demo com mudancas tecnicas das outras tracks.

## Status por track

| Track | Status | Proximo incremento recomendado |
| --- | --- | --- |
| Core compartilhado | implementing | `inc-01-baseline-env-status` |
| Tether | ready for promotion decision | decisao de promocao para `teste` |
| Cloak | standby curto ate baseline | `inc-01-privacy-metadata-audit` |
| Superteam Brasil | standby curto ate demo base | `inc-01-demo-script-and-seed-checklist` |

## Regras ativas para os proximos PRs

- Branch incremental sempre deve apontar para a branch especial da track.
- Branch incremental deve usar o padrao `track-inc/<slug-da-track>/<numero>-<escopo-curto>`.
- Branch especial so abre PR para `teste` depois dos gates de garantia.
- Se uma track ficar incompleta, status vira `standby` e nada parcial entra em `teste`.
- Frontend/UX exige evidencias visuais antes/depois.
- Checkout/escrow exige validacao de status, valor, moeda, permissao e documentacao de limites.

## Incrementos iniciados

| Incremento | Repositorio | Branch | Target planejado | Status |
| --- | --- | --- | --- | --- |
| Tether USDT currency parser | `p2ploot-frontend` | `track-inc/tether-usdt-escrow/01-usdt-currency-parser` | `track/tether-usdt-escrow` | integrado localmente na branch especial |
| Tether USDT smoke coverage | `p2ploot-backend` | `track-inc/tether-usdt-escrow/01-usdt-smoke-coverage` | `track/tether-usdt-escrow` | integrado localmente na branch especial |
| Tether stablecoin mint resolution | `p2ploot-backend` | `track-inc/tether-usdt-escrow/02-usdt-mint-resolution` | `track/tether-usdt-escrow` | integrado localmente na branch especial |
| Tether USDT demo seed | `p2ploot-backend` | `track-inc/tether-usdt-escrow/03-usdt-demo-seed` | `track/tether-usdt-escrow` | integrado localmente na branch especial |

Validacao do incremento frontend Tether:

- `git diff --check` no frontend: passou.
- `VITE_API_URL=http://localhost:6110 npm run build` no frontend: passou.
- Avisos conhecidos: Rollup remove anotacoes `/*#__PURE__*/` de dependencias `@privy-io/react-auth`/`ox`; nao bloqueou o build.
- Evidencia visual: pendente antes do PR, com modal de anuncio `USDT` aberto e detalhe tecnico do escrow mostrando `USDT`.

Validacao do incremento backend Tether:

- `git diff --check` no backend: passou.
- `node --check scripts/smoke-web3.js`: passou.
- `node --check scripts/smoke-web3-tether.js`: passou.
- Parse de `package.json`: passou.
- Smoke real `npm run smoke:web3:tether`: passou com backend local ativo em `http://localhost:6110`.

Validacao do incremento backend de stablecoin mint:

- `git diff --check` no backend: passou.
- `npm run build` no backend: passou apos restaurar dependencias locais com `npm install`.
- Avisos conhecidos: `npm install` reportou vulnerabilidades no grafo existente e o build avisou que `bigint` usou fallback JS puro; nenhum bloqueou a build.

Validacao smoke real Tether:

- Migracoes locais aplicadas com `npx prisma migrate deploy` antes do smoke, incluindo Web3 escrow, link Privy profile, delivery window e Cloak privacy.
- `npm run smoke:web3:tether` passou usando `currencySymbol: USDT`.
- O smoke validou caminhos de release, refund e cancelamento com `assetMint` resolvido para `Es9vMFrzaCERmJfrF4H2FYD4jU7VvSksQ88BNTc1NfQ`.
- Backend local foi encerrado apos a validacao.

Validacao de seed/demo e evidencia visual Tether:

- `node --check scripts/seed-web3-smoke-data.js`: passou.
- Seed local Web3 executado com `SMOKE_CURRENCY_SYMBOL=USDT`, criando anuncios ativos de demo em `USDT`.
- Backend local foi iniciado com `SOLANA_DEVNET_USDT_MINT=Es9vMFrzaCERmJfrF4H2FYD4jU7VvSksQ88BNTc1NfQ` para validar o caminho de checkout sem `assetMint` explicito.
- Checkout real pela UI criou escrow `funded` com `currencySymbol: USDT`, `amountUi: 10` e `assetMint: Es9vMFrzaCERmJfrF4H2FYD4jU7VvSksQ88BNTc1NfQ`.
- Evidencia desktop: `docs/hackathon-superteam/evidences/tether/tether-usdt-checkout-desktop.png`.
- Evidencia mobile: `docs/hackathon-superteam/evidences/tether/tether-usdt-checkout-mobile.png`.

## Gate atual da track Tether

Cumprido:

- Anuncio/checkout do frontend preserva `ad.currency` e nao troca `USDT` por `USDC` no escrow.
- Backend aceita e assertivamente testa `currencySymbol: USDT` no caminho smoke parametrizado.
- Backend resolve `assetMint` por moeda/rede quando o checkout cria escrow sem mint explicito.
- `.env.example` documenta mints `USDC`/`USDT` por devnet/mainnet, deixando devnet USDT configuravel.
- Smoke real `npm run smoke:web3:tether` passou contra backend local depois de aplicar migracoes pendentes.
- Seed Web3 aceita `SMOKE_CURRENCY_SYMBOL=USDT` para gerar anuncios de demo coerentes com a track.
- Evidencias visuais desktop/mobile confirmam checkout `USDT` com detalhe tecnico `VALOR 10 USDT`.
- Branches especiais locais de frontend e backend foram atualizadas com os incrementos validados.

Pendente antes de abrir PR da branch especial para `teste`:

- Confirmar `SOLANA_DEVNET_USDT_MINT` no ambiente de demo/deploy se a submissao Tether for demonstrada em devnet com SPL token real.
- Confirmar se a submissao Tether deve ficar em demo/devnet ou seguir para PR de promocao a `teste`.

## Observacoes de preflight

- O repositorio raiz nao possui branch `teste`; os subrepos `p2ploot-frontend` e `p2ploot-backend` possuem `origin/teste`.
- O repositorio raiz ja tinha alteracao local em `PLANO_IMPLEMENTACAO_TRACKS_SUPERTEAM.md` antes da aplicacao deste passo.
- O backend ja contem rotas e smoke tests Web3 para escrow.
- O frontend ja contem `USDT` em pontos do PlayerMarket, mas ainda precisa auditoria de ponta a ponta antes de marcar Tether como garantida.
