# Plano de governanca modular de branches para tracks Superteam

Projeto: P2P LOOT
Base investigada: `PLANO_IMPLEMENTACAO_TRACKS_SUPERTEAM.md` e `PLANO_SUBMISSAO_TRACKS_MINIMO.md`

## 1. Objetivo

Este plano define como implementar cada track Superteam de forma modular, revisavel e segura, sem obrigar que toda feature experimental entre imediatamente na branch de teste.

A regra central e:

> Cada track deve ter uma branch especial propria. Mudancas incrementais entram por PRs pequenos contra essa branch especial. So depois de garantida a funcionalidade a branch da track pode abrir PR para `teste`. Se a track nao for entrar agora em `teste`, ela permanece em standby na sua branch especial.

## 2. Leitura do plano atual

O plano existente ja separa bem as tracks por fases e prioridade:

- P0: `Tether`, `Cloak` e `Superteam Brasil`.
- P1: `Privacy`, `Solflare/QuickNode/Birdeye`, `RPC Fast`, `100xDevs` e `Adevar`.
- P2: `Jupiter`, `Dune` e `KIRAPAY`.
- P3: `theMiracle`, `Zerion` e `SagaPad`.

Tambem existe uma premissa forte: varias tracks reaproveitam o mesmo core de marketplace, checkout Web3, escrow, privacidade, RPC e evidencias de submissao. Por isso, a governanca precisa separar:

- mudancas de core reutilizavel;
- implementacoes especificas de cada track;
- demos/prototipos que podem ficar fora de `teste`;
- pacotes de documentacao/submissao que nao exigem merge de produto.

## 3. Modelo de branches

### 3.1. Branch de teste

Branch de destino para funcionalidades ja garantidas:

```text
teste
```

Regras:

- Recebe apenas features que passaram pelos gates de garantia.
- Nao deve receber prototipos incompletos, mocks ambiguos ou integracoes que ainda dependem de decisao.
- PR para `teste` deve sair da branch especial da track, nao de uma branch incremental solta.
- Se a feature precisa continuar desligada, deve entrar com feature flag documentada e padrao seguro.

### 3.2. Branch especial por track

Padrao:

```text
track/<slug-da-track>
```

Exemplos:

```text
track/tether-usdt-escrow
track/cloak-private-checkout
track/superteam-brasil-demo
track/privacy-identity-metadata
track/solana-rpc-live-checkout
track/adevar-audit-package
track/jupiter-any-token-checkout
track/dune-marketplace-analytics
```

Regras:

- A branch especial e a area de estabilizacao da track.
- Deve nascer da base aprovada para o ciclo, preferencialmente `origin/teste`.
- Pode receber varios PRs incrementais.
- Pode ficar em standby indefinidamente se a track nao for promovida agora.
- Deve conter ou apontar para um resumo da track, criterios de aceite, validacoes e pendencias.

### 3.3. Branch incremental de mudanca

Padrao:

```text
track-inc/<slug-da-track>/<numero>-<escopo-curto>
```

Exemplos:

```text
track-inc/tether-usdt-escrow/01-currency-model
track-inc/tether-usdt-escrow/02-checkout-ui
track-inc/cloak-private-checkout/01-privacy-flags
track-inc/cloak-private-checkout/02-private-receipt
track-inc/adevar-audit-package/01-threat-model
```

Regras:

- Sempre abrir PR da branch incremental para a branch especial da track.
- Nao abrir PR incremental diretamente para `teste`, exceto hotfix pequeno e explicitamente aprovado.
- Nao usar `track/<slug>/inc-...`, porque esse padrao conflita com a ref Git da branch especial `track/<slug>`.
- Cada incremento deve ter escopo pequeno, validacao propria e resumo claro.
- Um incremento deve ser reversivel sem derrubar a track inteira.
- Evitar misturar tracks no mesmo PR. Quando uma mudanca serve varias tracks, ela deve ir para uma branch de core compartilhado.

### 3.4. Branch de core compartilhado

Padrao recomendado:

```text
track/superteam-core
```

Uso:

- Checkout Web3 base.
- Escrow/timeline/eventos reutilizaveis.
- RPC configuravel e links de explorer.
- Feature flags e configuracao comum.
- Estrutura de docs de submissao compartilhada.

Regras:

- Criar apenas se houver mudanca realmente compartilhada por duas ou mais tracks.
- Track especifica pode partir dessa branch ou absorver commits dela via merge/rebase controlado.
- Se o core estiver pronto para produto, pode abrir PR proprio para `teste` antes das tracks.
- Se o core ainda for experimental, permanece como base tecnica para as branches de track.

## 4. Fluxo operacional

### 4.1. Criacao de uma track

1. Confirmar prioridade e escopo no plano de tracks.
2. Criar branch especial a partir da base aprovada:

```bash
git fetch origin
git switch teste
git pull --ff-only
git switch -c track/<slug-da-track>
git push -u origin track/<slug-da-track>
```

3. Criar um charter curto da track, no PR inicial ou em documento de apoio, contendo:

- objetivo da track;
- MVP minimo;
- demo esperada;
- criterios de garantia;
- arquivos/repositorios afetados;
- feature flags necessarias;
- evidencias esperadas;
- riscos e pendencias.

### 4.2. Incremento de implementacao

1. Atualizar a branch especial:

```bash
git switch track/<slug-da-track>
git pull --ff-only
```

2. Criar branch incremental:

```bash
git switch -c track-inc/<slug-da-track>/01-<escopo>
```

3. Implementar somente o escopo daquele incremento.
4. Validar localmente.
5. Abrir PR para:

```text
track/<slug-da-track>
```

6. Revisar e fazer merge na branch especial.
7. Atualizar status da track.

### 4.3. Promocao para teste

Quando a track estiver garantida:

```text
PR: track/<slug-da-track> -> teste
```

Antes desse PR, a branch especial precisa ter:

- build/testes relevantes passando;
- criterio funcional atendido;
- demo reproduzivel;
- documentacao curta;
- evidencias visuais ou tecnicas;
- limites conhecidos documentados;
- feature flag quando a feature nao deve ficar disponivel para todos;
- decisao explicita de que a track entra no ciclo de teste agora.

### 4.4. Standby

Se a track nao for promovida agora:

- manter a branch especial remota;
- fechar ou pausar PRs incrementais incompletos;
- registrar status como `standby`;
- documentar ultimo commit bom, pendencias e proximo incremento;
- nao fazer merge parcial em `teste`;
- antes de retomar, atualizar a branch contra `teste` e resolver conflitos em PR proprio.

## 5. Gates de garantia por track

Uma track so pode ser marcada como garantida quando cumprir todos os gates aplicaveis.

### 5.1. Gate comum

- A feature resolve o MVP minimo descrito no plano da track.
- O fluxo principal tem caminho feliz demonstravel.
- O fluxo de erro/falha mais provavel esta tratado ou documentado.
- A alteracao tem README, roteiro de demo ou secao de submissao.
- Builds/testes relevantes foram executados.
- Mudancas frontend/UX possuem evidencias visuais quando aplicavel.
- Integracoes externas usam variaveis de ambiente e nao expõem segredo.
- A branch nao contem mudancas aleatorias fora da track.
- O PR de promocao explica claramente por que a feature esta pronta para `teste`.

### 5.2. Gate de checkout/escrow

Aplicavel a `Tether`, `Cloak`, `Privacy`, `Jupiter`, `KIRAPAY`, `Solflare/RPC` e partes de `Superteam Brasil`.

- Criacao ou simulacao honesta de escrow esta clara.
- Status do escrow segue estados esperados.
- Valor, moeda, taxa e total sao exibidos de forma consistente.
- Confirmacao on-chain, devnet ou demo controlada esta documentada.
- Links de explorer/logs aparecem quando houver transacao real.
- Permissoes de comprador/vendedor nao ficam mais abertas do que antes.
- Reembolso, disputa ou limitacao desses fluxos esta documentada.

### 5.3. Gate de privacidade

Aplicavel a `Cloak` e `Privacy`.

- O que e publico e privado esta claro na UI ou documentacao.
- Wallets e metadados sensiveis sao mascarados quando prometido.
- Metadata minima no escrow esta definida.
- Se for prototipo, a diferenca entre demo e integracao real esta explicita.
- Nenhuma promessa de privacidade de producao e feita sem evidencia tecnica.

### 5.4. Gate de infraestrutura Web3

Aplicavel a `RPC Fast`, `Solflare/QuickNode/Birdeye`, `Jupiter`, `Tether` e `Dune`.

- RPC e configuravel por ambiente.
- Falha de provider tem mensagem ou fallback aceitavel.
- Retentativa/backoff existe quando fizer parte do fluxo.
- Mints, decimals, rede e explorer estao documentados.
- Dados de preco/cotacao indicam fonte e limite de confianca.

### 5.5. Gate de documentacao/auditoria

Aplicavel a `Adevar`, `Dune`, `Superteam Brasil` e qualquer track de submissao.

- Escopo da track esta documentado.
- Riscos e limitacoes estao documentados.
- Existe roteiro de demonstracao.
- Existem screenshots, logs, dashboard, queries ou evidencias equivalentes.
- Invariantes de seguranca estao listadas quando houver escrow/pagamento.

## 6. Matriz de branches recomendadas

| Track | Branch especial | Prioridade | Quando promover para `teste` |
| --- | --- | --- | --- |
| Core compartilhado | `track/superteam-core` | P0 tecnico | Checkout/escrow/RPC base estiverem reutilizaveis e sem acoplamento de demo |
| Tether | `track/tether-usdt-escrow` | P0 | USDT aparece no anuncio/checkout/escrow e a demo de pagamento esta reproduzivel |
| Cloak | `track/cloak-private-checkout` | P0 | Compra privada tem UI, metadata minima, mascaramento e limites documentados |
| Superteam Brasil | `track/superteam-brasil-demo` | P0 | Demo PT-BR, seed, roteiro, README e fluxo principal estao fechados |
| Privacy | `track/privacy-identity-metadata` | P1 | Reaproveita Cloak e entrega alias/mascaramento sem mexer no auth principal |
| Solflare/QuickNode/Birdeye | `track/solana-rpc-live-checkout` | P1 | Wallet/RPC/explorer/cotacao estao demonstraveis sem fragilizar checkout |
| RPC Fast | `track/rpc-fast-confirmation` | P1 | Healthcheck, retry/status tecnico e logs de confirmacao estao prontos |
| 100xDevs | `track/fullstack-polish-100xdevs` | P1 | Produto full-stack tem fluxo ponta a ponta e documentacao curta |
| Adevar | `track/adevar-audit-package` | P1 | Threat model, invariantes, escopo de auditoria e testes minimos estao prontos |
| Jupiter | `track/jupiter-any-token-checkout` | P2 | Quote/swap demo esta claro e nao bloqueia fluxo stablecoin principal |
| Dune | `track/dune-marketplace-analytics` | P2 | Dashboard/query/plano de eventos tem evidencias e narrativa de metricas |
| KIRAPAY | `track/kirapay-checkout` | P2 | Payment link/callback/status mapping funcionam ou o prototipo esta honesto |
| theMiracle | `track/themiracle-wallet-benefit` | P3 | Elegibilidade validada e beneficio in-wallet demonstravel |
| Zerion | `track/zerion-escrow-agent` | P3 | Agente monitora escrows ou gera relatorio operacional demonstravel |
| SagaPad | `track/sagapad-growth-agent` | P3 | Agente gera campanha/calendario com exemplos prontos para submissao |

## 7. Ordem recomendada

### Bloco 0 - Governanca e baseline

- Criar `track/superteam-core`, se o core ainda nao estiver pronto em `teste`.
- Criar estrutura de docs de submissao.
- Confirmar variaveis `.env.example`.
- Definir feature flags das tracks experimentais.

### Bloco 1 - Tracks P0

1. `track/tether-usdt-escrow`
2. `track/cloak-private-checkout`
3. `track/superteam-brasil-demo`

Essas tracks compartilham o mesmo argumento: marketplace gamer com escrow, stablecoin e privacidade opcional.

### Bloco 2 - Tracks P1 de sustentacao

1. `track/privacy-identity-metadata`, derivada de Cloak.
2. `track/rpc-fast-confirmation`, sustentando checkout confiavel.
3. `track/solana-rpc-live-checkout`, com wallet/RPC/cotacao.
4. `track/adevar-audit-package`, documentando seguranca.

### Bloco 3 - Expansoes P2/P3

- `Jupiter`, se quote/swap couber em incremento pequeno.
- `Dune`, se houver dados on-chain ou plano de queries forte.
- `KIRAPAY`, se API/callback forem simples.
- `theMiracle`, `Zerion` e `SagaPad`, somente apos validar elegibilidade e custo.

## 8. PRs e revisao

Todo PR incremental para uma branch especial deve informar:

```md
## Track

- Track:
- Branch especial:
- Incremento:
- Target do PR:

## Escopo

- O que muda:
- O que nao muda:
- Feature flag:

## Criterios de garantia afetados

- [ ] MVP da track
- [ ] Demo
- [ ] Validacao tecnica
- [ ] Evidencias
- [ ] Documentacao

## Validacao

- [ ] Build/teste:
- [ ] Evidencia visual:
- [ ] Logs/explorer/dashboard:

## Status apos merge

- [ ] Continua implementando
- [ ] Pronta para revisao da track
- [ ] Pronta para PR para `teste`
- [ ] Standby
```

## 9. Skills/workflows necessarios

### 9.1. Workflow obrigatorio novo

Criar e usar:

```text
.windsurf/workflows/superteam-track-branch-governance.md
```

Uso:

- antes de criar branch especial;
- antes de iniciar incremento;
- antes de abrir PR para branch especial;
- antes de promover uma track para `teste`;
- antes de colocar uma track em standby.

### 9.2. Workflows existentes que continuam aplicaveis

Quando a track tocar frontend/UX:

- `.windsurf/workflows/frontend-increment-preflight.md`
- `.windsurf/workflows/frontend-visual-evidence-pack.md`
- `.windsurf/workflows/frontend-review-bundle.md`

Quando a track exigir extracao/modularizacao:

- `.windsurf/workflows/frontend-extraction-playbook.md`

### 9.3. Skills futuras opcionais

Criar novos workflows/skills somente se o trabalho se repetir pelo menos duas vezes:

- `web3-integration-preflight`: para validar docs oficiais, rede, mints, envs, secrets, RPC e explorer antes de integrar parceiros.
- `track-submission-pack`: para padronizar README, roteiro de video, screenshots, links e limitacoes por track.
- `escrow-security-review`: para revisar invariantes, permissao, status terminal, disputa, refund e release antes de promover checkout/escrow.

Por enquanto, o workflow novo de governanca de tracks mais os workflows de frontend existentes sao suficientes.

## 10. Decisao final

O fluxo recomendado para o P2P LOOT e:

1. Trabalhar cada track em branch especial propria.
2. Fazer mudancas incrementais por PR contra essa branch especial.
3. Garantir a track com gates funcionais, tecnicos, visuais e documentais.
4. Promover para `teste` apenas quando a feature deve entrar no ciclo de homologacao.
5. Manter em standby qualquer track util para submissao, mas ainda inadequada para `teste`.

Isso permite aplicar para varias tracks sem transformar a branch de teste em deposito de experimentos e sem perder trabalho que pode ser retomado depois.
