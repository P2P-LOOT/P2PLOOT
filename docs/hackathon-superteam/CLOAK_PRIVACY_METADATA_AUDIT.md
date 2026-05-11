# Cloak privacy metadata audit

Branch alvo dos subrepos: `track/cloak-private-checkout`

## Objetivo do incremento

Validar se a track Cloak pode prometer, de forma honesta, privacidade operacional no checkout atual.

O escopo deste incremento e o MVP de privacidade: registrar intencao Cloak, mascarar identidades quando solicitado e manter metadata de escrow no minimo necessario. A integracao de settlement shielded completa continua isolada como caminho futuro.

## Estado encontrado

Backend:

- `src/lib/cloak-privacy.ts` define provider, fee model e config devnet.
- `GET /api/web3/privacy/status` declara modo `privacy_intent_ready` e deixa claro que shielded settlement adapter ainda nao esta ativo.
- `POST /api/web3/escrows` grava `metadata.cloakPrivacy` com flags de vendedor/comprador.
- `GET /api/ads/[id]/conversation` mascara comprador como `Comprador privado via Cloak` para vendedor quando metadata indica `buyerRequested`.
- `market_ads.cloak_seller_privacy_enabled` existe via migracao `20260508090000_add_cloak_seller_privacy`.

Frontend:

- Card do marketplace mostra badge `Cloak` quando o vendedor ativou privacidade.
- Modal do anuncio mascara vendedor como `Vendedor privado via Cloak` quando aplicavel.
- Checkout envia `metadata.cloakPrivacy` quando vendedor ou comprador tem privacidade ativa.
- Conversas do vendedor exibem comprador privado quando backend retorna `buyer_privacy_requested`.

## Ajuste aplicado neste incremento

O helper `buildCloakPrivacyMetadata` foi reduzido para metadata operacional minima no escrow:

- `version`
- `enabled`
- `sellerRequested`
- `buyerRequested`
- `provider`
- `mode`
- `settlement`
- `feeModel.fixedFeeSol`
- `feeModel.variableFeeBps`

Ficaram fora do metadata persistido por escrow:

- URLs de documentacao.
- Config completa de devnet.
- Nome de pacote SDK.
- Texto de disclosure longo.

Essas informacoes continuam disponiveis no endpoint de status/diagnostico, que e o lugar adequado para configuracao e documentacao.

## Limites honestos da track

- O produto registra e demonstra intencao de privacidade Cloak.
- O produto mascara identidade publica do vendedor e do comprador nos pontos auditados.
- O produto ainda nao executa settlement shielded UTXO completo via SDK Cloak.
- O status endpoint deve ser usado na demo para explicar que o adapter real ainda esta desativado.

## Gates antes de promocao para `teste`

- Build backend na branch Cloak passando.
- Smoke `npm run smoke:cloak` passando com escrow `metadata.cloakPrivacy.enabled: true` e comprador mascarado para vendedor.
- Evidencia visual de anuncio com vendedor privado e conversa com comprador privado.
- Confirmar copy de UI para nao prometer privacidade de producao alem do que foi implementado.
