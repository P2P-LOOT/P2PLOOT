# Superteam track matrix

## Tracks P0

| Track | Entrega demonstravel | Evidencia atual | Status de promocao |
| --- | --- | --- | --- |
| Superteam Brasil | Produto completo em PT-BR com marketplace, chat, escrow, stablecoin e privacidade opcional | README + demo script + evidencias Tether/Cloak | pronta para demo review |
| Tether | Checkout/escrow em `USDT` com mint resolvido por ambiente | `evidences/tether/tether-usdt-checkout-desktop.png`, `evidences/tether/tether-usdt-checkout-mobile.png` | ready for promotion decision |
| Cloak | Privacy intent + mascaramento de comprador/vendedor | `evidences/cloak/cloak-private-buyer-desktop.png`, `evidences/cloak/cloak-private-seller-mobile.png` | ready for promotion decision |

## Tracks reaproveitaveis

| Track | Como reaproveita a demo unica | Condicao antes de submissao forte |
| --- | --- | --- |
| Privacy | Usa o mesmo fluxo Cloak-ready para explicar identidade/wallet protegida | Pronta para demo review como Privacy via Cloak-ready; MagicBlock/SNS ficam em standby |
| RPC / Solflare / QuickNode / Birdeye | Usa status tecnico, wallet e explorer/status do checkout | RPC health pronto; Birdeye/Solflare visual ficam como incrementos separados |
| Adevar | Usa o fluxo escrow como caso auditavel | Pacote inicial criado em `AUDIT_SCOPE_P2PLOOT_ESCROW.md`; testes minimos ficam como proximo incremento |
| Dune | Usa eventos de escrow/anuncios como fonte analitica | Definir queries ou export minimo de metricas |
| Jupiter | Pode entrar como extensao de quote/swap | Nao misturar se atrasar a demo P0 |
| KIRAPAY | Pode entrar como trilha alternativa de pagamento | Depende de callback/webhook e criterio de seguranca |

## Gates comuns antes de video

- Fluxo principal reproduzivel do anuncio ate o escrow.
- Moeda e valor consistentes entre anuncio, checkout e detalhe tecnico.
- Privacy copy honesta e sem promessa acima da implementacao.
- Evidencias desktop/mobile atualizadas.
- Limites conhecidos documentados no README ou no roteiro.
- Decisao registrada: promover para `teste` ou manter em standby.
