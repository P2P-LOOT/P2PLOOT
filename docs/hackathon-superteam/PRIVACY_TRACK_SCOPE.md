# Privacy track scope

Branch alvo dos subrepos: `track/privacy-identity-metadata`

## Objetivo

Mostrar que o P2P LOOT tem um problema nativo de privacidade em marketplaces gamer P2P e uma abordagem de produto para reduzir exposicao de identidade, wallet e metadata publica durante uma negociacao.

Esta track reaproveita a entrega Cloak-ready ja validada. Ela nao implementa MagicBlock, SNS ou novo protocolo de identidade neste incremento.

## MVP garantido agora

- Vendedor pode aparecer como `Vendedor privado via Cloak` no anuncio/checkout.
- Comprador pode aparecer como `Comprador privado via Cloak` para o vendedor.
- Escrow registra `metadata.cloakPrivacy` minima.
- Copy do produto limita a promessa a identidade publica ocultada/mascarada.
- Evidencias visuais Cloak mostram comprador e vendedor mascarados.

## O que fica fora agora

- Integracao MagicBlock realtime/ephemeral.
- Campo SNS `.sol` como alias de identidade.
- Novo sistema de identidade.
- Criptografia ponta a ponta de chat.
- Settlement shielded completo.

Esses itens so devem virar implementacao depois de uma decisao explicita de provider e validacao de documentacao oficial.

## Reaproveitamento da track Cloak

| Necessidade Privacy | Cobertura atual |
| --- | --- |
| Mascarar vendedor em contexto publico | `Vendedor privado via Cloak` no modal do anuncio |
| Mascarar comprador para vendedor | `Comprador privado via Cloak` na lista/conversa |
| Reduzir metadata sensivel | `buildCloakPrivacyMetadata` guarda somente campos operacionais |
| Documentar limite honesto | `CLOAK_PRIVACY_METADATA_AUDIT.md` diferencia privacy intent de settlement shielded |
| Evidencia visual | `evidences/cloak/*` |

## Demo sugerida

1. Abrir marketplace em PT-BR.
2. Mostrar anuncio com privacidade ativa.
3. Abrir detalhe como comprador.
4. Apontar vendedor mascarado.
5. Iniciar ou abrir conversa.
6. Mostrar comprador mascarado para o vendedor.
7. Explicar que o MVP reduz exposicao publica enquanto o protocolo profundo fica como evolucao.

## Criterios de pronto para submissao Privacy

- README/roteiro explicam o problema de privacidade no marketplace gamer.
- Evidencia visual do vendedor privado e comprador privado existe.
- Metadata de escrow nao inclui config longa, docs ou disclosure repetido.
- UI nao promete privacidade criptografica completa.
- Provider externo opcional fica claramente marcado como futuro.

## Decisao de gate

A track esta pronta para demo review como `Privacy via Cloak-ready`.

Ela deve ficar em standby para implementacao profunda ate existir decisao explicita entre MagicBlock, SNS ou outro provider, com docs atuais revisadas antes de qualquer codigo.
