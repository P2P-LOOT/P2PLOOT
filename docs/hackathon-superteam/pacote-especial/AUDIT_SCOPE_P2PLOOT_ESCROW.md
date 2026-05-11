# Audit scope - P2P LOOT escrow

Branch alvo dos subrepos: `track/adevar-audit-package`

## Objetivo Adevar

Mostrar que o P2P LOOT tem uma superficie de seguranca relevante, auditavel e bem delimitada para escrow/pagamentos P2P.

Este pacote nao cria contrato novo e nao reescreve backend. Ele transforma o fluxo atual em material de auditoria: arquitetura, permissoes, status, invariantes, riscos e escopo recomendado.

## Arquitetura em escopo

Backend:

- `src/app/api/web3/escrows/route.ts`
  - lista escrows visiveis ao usuario;
  - cria draft de escrow a partir de conversa;
  - registra snapshot do anuncio, valor, moeda, rede, fee e metadata.
- `src/app/api/web3/escrows/[id]/route.ts`
  - le escrow individual;
  - aplica transicoes de status;
  - registra eventos e assinaturas;
  - executa caminhos devnet/demo quando habilitados.
- `src/lib/web3.ts`
  - normalizacao de rede;
  - checagem de admin;
  - leitura autorizada;
  - status terminais.
- `src/lib/solana-rpc.ts`
  - validacao opcional de assinatura Solana via RPC.
- `src/lib/escrow-auto-release.ts`
  - auto release apos janela de confirmacao do comprador.
- `src/lib/solana-devnet-demo.ts` e `src/lib/solana-anchor-escrow-demo.ts`
  - caminhos demo/devnet de transacao.

Frontend:

- `src/components/PlayerMarket/AdDetailsModal.jsx`
  - orquestra checkout, conversa, estados do escrow, acoes de comprador/vendedor e detalhes tecnicos.

## Fluxo simples

```text
Anuncio ativo
  -> Comprador inicia conversa
  -> Comprador cria escrow draft
  -> Comprador registra deposito
  -> Vendedor confirma entrega
  -> Comprador libera pagamento
  -> Conversa e reputacao sao atualizadas
```

Fluxos alternativos:

```text
funded/seller_confirmed -> request_refund -> record_refund -> refunded
funded/seller_confirmed/refund_requested -> open_dispute -> disputed
draft/initialized -> cancel -> cancelled
seller_confirmed + janela expirada -> auto_released -> released
```

## Permissoes atuais

| Acao | Quem pode executar | Observacao |
| --- | --- | --- |
| Listar escrows | comprador, vendedor, criador ou admin | filtro aplicado por usuario, conversa, status e anuncio |
| Ler escrow | comprador, vendedor, criador ou admin | `canReadEscrow` centraliza a checagem |
| Criar escrow | comprador ou admin | vendedor nao cria draft, exceto se admin |
| Registrar deposito | comprador ou admin | exige assinatura efetiva |
| Confirmar entrega | vendedor ou admin | somente de `funded` para `seller_confirmed` |
| Liberar pagamento | comprador ou admin | de `funded`/`seller_confirmed` para `released` |
| Solicitar reembolso | comprador, vendedor ou admin | de `funded`/`seller_confirmed` |
| Registrar reembolso | comprador ou admin | de `funded`/`seller_confirmed`/`refund_requested`/`disputed` |
| Abrir disputa | comprador, vendedor ou admin | de `funded`/`seller_confirmed`/`refund_requested` |
| Cancelar draft | comprador, criador ou admin | somente de `draft`/`initialized` |

## Status do escrow

Ativos:

- `draft`
- `initialized`
- `funded`
- `seller_confirmed`
- `refund_requested`
- `disputed`

Terminais:

- `released`
- `refunded`
- `cancelled`

## Invariantes para auditoria

- Apenas participantes da conversa ou admin podem ler um escrow.
- Apenas comprador ou admin podem criar escrow draft.
- Apenas comprador ou admin podem registrar deposito.
- Apenas vendedor ou admin podem confirmar entrega.
- Apenas comprador ou admin podem liberar pagamento.
- Status terminal nao pode voltar para status ativo.
- `release` e `record_refund` exigem assinatura efetiva.
- Quando `SOLANA_VALIDATE_TX_SIGNATURES=true`, assinatura precisa existir no RPC e estar valida.
- `open_dispute` impede auto release porque o auto release so busca `seller_confirmed`.
- Auto release deve registrar evento `auto_released` e atualizar conversa/reputacao uma unica vez.
- Valor/moeda/rede do deal precisam permanecer consistentes com snapshot do anuncio ou payload validado.

## Riscos e mitigacoes atuais

| Risco | Mitigacao atual | Pendente recomendado |
| --- | --- | --- |
| Comprador falso registra deposito sem transacao real | `txSignature` obrigatoria; validacao RPC opcional | ligar `SOLANA_VALIDATE_TX_SIGNATURES=true` em ambientes reais |
| Replay de assinatura | indice por `depositTx` existe no modelo | checar unicidade/nao reutilizacao por action antes de aceitar |
| Manipulacao de valor/moeda no payload | snapshot do anuncio fica em metadata | validar `amountUi`, `currencySymbol` e `assetMint` contra anuncio/mint por rede |
| Vendedor tenta liberar pagamento | rota bloqueia `release` para nao comprador | manter smoke de permissao |
| Comprador tenta confirmar entrega pelo vendedor | rota bloqueia `seller_confirm` para nao vendedor | manter smoke de permissao |
| Admin abusivo altera estados | admin bypass existe por design | auditar logs/admin actions e separar permissao operacional |
| Disputa ignorada pelo auto release | auto release so processa `seller_confirmed` | adicionar teste explicito de disputa bloqueando auto release |
| Metadata privada excessiva | Cloak ja reduziu metadata persistida na branch propria | manter contrato minimo de metadata por track |
| Falha RPC no checkout | track RPC adicionou `rpcHealth` e chip visual | exigir RPC saudavel antes de demo live |

## Evidencias ja disponiveis

- Tether smoke real: `npm run smoke:web3:tether` na branch Tether.
- Cloak smoke real: `npm run smoke:cloak` na branch Cloak.
- RPC status vivo: `npm run web3:status` na branch RPC.
- Evidencias visuais:
  - `docs/hackathon-superteam/evidences/tether/tether-usdt-checkout-desktop.png`
  - `docs/hackathon-superteam/evidences/tether/tether-usdt-checkout-mobile.png`
  - `docs/hackathon-superteam/evidences/cloak/cloak-private-buyer-desktop.png`
  - `docs/hackathon-superteam/evidences/cloak/cloak-private-seller-mobile.png`
  - `docs/hackathon-superteam/evidences/rpc/rpc-health-checkout-desktop.png`
  - `docs/hackathon-superteam/evidences/rpc/rpc-health-checkout-mobile.png`

## Escopo recomendado para auditoria externa

Prioridade alta:

- Permissoes de leitura/criacao/transicao de escrow.
- Maquina de estados e bloqueio de regressao de status terminal.
- Validacao de assinatura Solana e replay.
- Consistencia de valor, moeda, mint e rede.
- Auto release e disputa.
- Efeito de release/refund na conversa e reputacao.

Prioridade media:

- Privacidade de metadata e mascaramento de identidades.
- Logs de eventos e rastreabilidade para suporte.
- Feature flags de devnet/mainnet e fallback local.
- Admin bypass e segregacao de permissoes.

Fora do escopo deste pacote:

- Contrato on-chain novo.
- Auditoria de front-end visual completa.
- Integracoes futuras como Jupiter, Birdeye e KIRAPAY.

## Checklist de pronto Adevar

- Documento de escopo criado.
- Threat model inicial documentado.
- Invariantes listadas.
- Permissoes mapeadas por rota/acao.
- Riscos e mitigacoes descritos.
- Evidencias das tracks Tether/Cloak/RPC referenciadas.
- Testes adicionais de permissao/status definidos como proximo incremento, nao bloqueantes para pacote inicial.
