# P2P LOOT - Superteam Brasil submission

## Tese

P2P LOOT e um marketplace brasileiro para compra e venda segura de ativos digitais de games usando Web3.

O produto resolve um problema simples: jogadores negociam contas, itens, servicos e assets digitais em canais informais, com alto risco de golpe, exposicao de identidade e pagamento sem garantia. A proposta do P2P LOOT e colocar conversa, reputacao, checkout, escrow e privacidade opcional no mesmo fluxo.

## Demo unica

A demo de Superteam Brasil reaproveita as tracks ja validadas:

- `Tether`: checkout em `USDT` com escrow Web3.
- `Cloak`: privacidade de identidade no anuncio e na conversa.
- `Core`: marketplace gamer, chat, reputacao, status de escrow e evidencias.

Fluxo recomendado:

1. Vendedor cria ou usa um anuncio gamer ativo.
2. Anuncio aparece com moeda `USDT`.
3. Comprador abre o anuncio e ve seller privacy quando ativada.
4. Comprador inicia conversa.
5. Comprador cria escrow em `USDT`.
6. Sistema mostra status tecnico do escrow.
7. Vendedor visualiza comprador mascarado quando privacy foi solicitada.
8. Narrativa fecha com reputacao, seguranca e proximos passos.

## Publico

- Gamers brasileiros que compram e vendem ativos digitais.
- Guildas e comunidades que precisam reduzir risco em negociacoes P2P.
- Builders Solana interessados em comercio gamer com stablecoin e UX local.

## Diferenciais

- Produto full-stack demonstravel, nao apenas pitch.
- Narrativa brasileira clara: comercio gamer informal virando fluxo seguro.
- Stablecoin no checkout para reduzir friccao de preco.
- Privacidade opcional para comprador e vendedor.
- Arquitetura modular por tracks, permitindo promover ou pausar cada entrega sem misturar prototipos na branch de teste.

## Evidencias atuais

- Tether desktop: `docs/hackathon-superteam/evidences/tether/tether-usdt-checkout-desktop.png`.
- Tether mobile: `docs/hackathon-superteam/evidences/tether/tether-usdt-checkout-mobile.png`.
- Cloak comprador desktop: `docs/hackathon-superteam/evidences/cloak/cloak-private-buyer-desktop.png`.
- Cloak vendedor mobile: `docs/hackathon-superteam/evidences/cloak/cloak-private-seller-mobile.png`.
- RPC checkout desktop: `docs/hackathon-superteam/evidences/rpc/rpc-health-checkout-desktop.png`.
- RPC checkout mobile: `docs/hackathon-superteam/evidences/rpc/rpc-health-checkout-mobile.png`.

## Limites honestos

- O fluxo Tether valida `USDT` em demo/local com mint configuravel por ambiente.
- A entrega Cloak atual e privacy intent + mascaramento de identidade; settlement shielded completo ainda depende de adapter real.
- A demo Superteam Brasil nao deve adicionar integracoes novas so para submissao.
- A promocao para `teste` deve acontecer apenas se a decisao for levar o pacote completo para ciclo de QA.

## Proximos passos

- Confirmar ambiente de demo/deploy.
- Rodar roteiro fim a fim antes do video.
- Capturar screenshots finais se o layout ou dados de seed mudarem.
- Decidir se Superteam Brasil entra em `teste` como pacote de demo ou fica em standby na branch especial.
