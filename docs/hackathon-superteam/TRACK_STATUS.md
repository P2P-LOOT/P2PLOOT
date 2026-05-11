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
| Tether | implementing | evidencia visual + smoke `USDT` com backend local |
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
- Smoke real `npm run smoke:web3:tether`: pendente porque exige backend local ativo em `API_BASE`.

## Gate atual da track Tether

Cumprido:

- Anuncio/checkout do frontend preserva `ad.currency` e nao troca `USDT` por `USDC` no escrow.
- Backend aceita e assertivamente testa `currencySymbol: USDT` no caminho smoke parametrizado.
- Branches especiais locais de frontend e backend foram atualizadas com os incrementos validados.

Pendente antes de marcar `guaranteed`:

- Rodar smoke real com backend local e dados seedados: `npm run smoke:web3:tether`.
- Capturar evidencia visual desktop/mobile do checkout de anuncio `USDT`.
- Confirmar se a submissao Tether deve ficar em demo/devnet ou seguir para PR de promocao a `teste`.

## Observacoes de preflight

- O repositorio raiz nao possui branch `teste`; os subrepos `p2ploot-frontend` e `p2ploot-backend` possuem `origin/teste`.
- O repositorio raiz ja tinha alteracao local em `PLANO_IMPLEMENTACAO_TRACKS_SUPERTEAM.md` antes da aplicacao deste passo.
- O backend ja contem rotas e smoke tests Web3 para escrow.
- O frontend ja contem `USDT` em pontos do PlayerMarket, mas ainda precisa auditoria de ponta a ponta antes de marcar Tether como garantida.
